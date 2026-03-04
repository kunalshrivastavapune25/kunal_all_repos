# Building a Personal AI Assistant with Gradio and LLM Evaluation

This lab demonstrates how to create an interactive AI assistant that represents a real person (you!) based on your LinkedIn profile and a personal summary. We'll build a Gradio chat interface, then add an intelligent evaluation loop that checks the quality of responses and automatically retries if they don't meet the standard—all without using an agentic framework.

## Overview

1. **Extract text** from a LinkedIn PDF.
2. **Load a personal summary** from a text file.
3. **Construct a system prompt** that instructs the LLM to act as you.
4. **Build a chat interface** using Gradio.
5. **Add a quality evaluator** (another LLM) that judges responses.
6. **Implement a retry mechanism** that feeds back evaluation results to improve answers.

---

## Setup

Install required packages:

```bash
pip install openai pypdf gradio pydantic python-dotenv
```

*Optional:* If you plan to use a non-OpenAI evaluator (e.g., Gemini), install their SDK or use an OpenAI-compatible endpoint as shown.

---

## Step 1: Prepare Your Personal Data

Place your own files in a folder called `me`:

- `linkedin.pdf` – a PDF export of your LinkedIn profile.
- `summary.txt` – a short personal summary (career highlights, skills, etc.).

---

## Step 2: Load and Process the Data

```python
from dotenv import load_dotenv
from openai import OpenAI
from pypdf import PdfReader
import gradio as gr

load_dotenv(override=True)
openai = OpenAI()

# Load LinkedIn PDF
reader = PdfReader("me/linkedin.pdf")
linkedin = ""
for page in reader.pages:
    text = page.extract_text()
    if text:
        linkedin += text

print("LinkedIn text extracted (first 500 chars):", linkedin[:500])

# Load summary
with open("me/summary.txt", "r", encoding="utf-8") as f:
    summary = f.read()
```

---

## Step 3: Build the System Prompt

The system prompt instructs the LLM to act as you, using your summary and LinkedIn data as context.

```python
name = "Your Name"  # Replace with your actual name

system_prompt = f"You are acting as {name}. You are answering questions on {name}'s website, \
particularly questions related to {name}'s career, background, skills and experience. \
Your responsibility is to represent {name} for interactions on the website as faithfully as possible. \
You are given a summary of {name}'s background and LinkedIn profile which you can use to answer questions. \
Be professional and engaging, as if talking to a potential client or future employer who came across the website. \
If you don't know the answer, say so."

system_prompt += f"\n\n## Summary:\n{summary}\n\n## LinkedIn Profile:\n{linkedin}\n\n"
system_prompt += f"With this context, please chat with the user, always staying in character as {name}."
```

---

## Step 4: Create a Basic Gradio Chat Interface

Define a simple chat function that calls the LLM with the system prompt and conversation history.

```python
def chat(message, history):
    # Note: Gradio's history format may need cleaning for some providers (see note below)
    messages = [{"role": "system", "content": system_prompt}] + history + [{"role": "user", "content": message}]
    response = openai.chat.completions.create(
        model="gpt-4o-mini",  # Use a model you have access to
        messages=messages
    )
    return response.choices[0].message.content

# Launch the interface
gr.ChatInterface(chat).launch()
```

> **Note for non-OpenAI providers:** Gradio may add extra fields to the history dictionary. If your provider (e.g., Groq) complains, clean the history first:
> ```python
> history = [{"role": h["role"], "content": h["content"]} for h in history]
> ```

---

## Step 5: Add a Quality Evaluator

We'll use a separate LLM (here, Google Gemini via OpenAI-compatible endpoint) to evaluate whether the assistant's response is acceptable.

### Define a Pydantic model for the evaluation result

```python
from pydantic import BaseModel

class Evaluation(BaseModel):
    is_acceptable: bool
    feedback: str
```

### Create the evaluator system prompt

```python
evaluator_system_prompt = f"You are an evaluator that decides whether a response to a question is acceptable. \
You are provided with a conversation between a User and an Agent. Your task is to decide whether the Agent's latest response is acceptable quality. \
The Agent is playing the role of {name} and is representing {name} on their website. \
The Agent has been instructed to be professional and engaging, as if talking to a potential client or future employer who came across the website. \
The Agent has been provided with context on {name} in the form of their summary and LinkedIn details. Here's the information:"

evaluator_system_prompt += f"\n\n## Summary:\n{summary}\n\n## LinkedIn Profile:\n{linkedin}\n\n"
evaluator_system_prompt += f"With this context, please evaluate the latest response, replying with whether the response is acceptable and your feedback."
```

### Function to generate the evaluator user prompt

```python
def evaluator_user_prompt(reply, message, history):
    user_prompt = f"Here's the conversation between the User and the Agent: \n\n{history}\n\n"
    user_prompt += f"Here's the latest message from the User: \n\n{message}\n\n"
    user_prompt += f"Here's the latest response from the Agent: \n\n{reply}\n\n"
    user_prompt += "Please evaluate the response, replying with whether it is acceptable and your feedback."
    return user_prompt
```

### Set up the evaluator client (using Gemini as an example)

```python
import os
gemini = OpenAI(
    api_key=os.getenv("GOOGLE_API_KEY"),
    base_url="https://generativelanguage.googleapis.com/v1beta/openai/"
)
```

### Evaluation function

```python
def evaluate(reply, message, history) -> Evaluation:
    messages = [
        {"role": "system", "content": evaluator_system_prompt},
        {"role": "user", "content": evaluator_user_prompt(reply, message, history)}
    ]
    response = gemini.beta.chat.completions.parse(
        model="gemini-1.5-flash",  # or "gemini-2.0-flash-exp"
        messages=messages,
        response_format=Evaluation
    )
    return response.choices[0].message.parsed
```

---

## Step 6: Retry Logic for Unacceptable Responses

If the evaluator marks a response as unacceptable, we rerun the LLM with additional context explaining why the previous attempt was rejected.

```python
def rerun(reply, message, history, feedback):
    updated_system_prompt = system_prompt + "\n\n## Previous answer rejected\nYou just tried to reply, but the quality control rejected your reply\n"
    updated_system_prompt += f"## Your attempted answer:\n{reply}\n\n"
    updated_system_prompt += f"## Reason for rejection:\n{feedback}\n\n"
    messages = [{"role": "system", "content": updated_system_prompt}] + history + [{"role": "user", "content": message}]
    response = openai.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages
    )
    return response.choices[0].message.content
```

---

## Step 7: Integrate Evaluation into the Chat Function

Now modify the chat function to evaluate each response and retry if needed.

```python
def chat(message, history):
    # Optional: add special behavior (e.g., pig latin for certain keywords)
    if "patent" in message.lower():
        system = system_prompt + "\n\nEverything in your reply needs to be in pig latin - \
              it is mandatory that you respond only and entirely in pig latin"
    else:
        system = system_prompt

    messages = [{"role": "system", "content": system}] + history + [{"role": "user", "content": message}]
    response = openai.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages
    )
    reply = response.choices[0].message.content

    # Evaluate the reply
    evaluation = evaluate(reply, message, history)

    if evaluation.is_acceptable:
        print("Passed evaluation - returning reply")
    else:
        print("Failed evaluation - retrying")
        print("Feedback:", evaluation.feedback)
        reply = rerun(reply, message, history, evaluation.feedback)

    return reply
```

---

## Step 8: Launch the Enhanced Chat Interface

```python
gr.ChatInterface(chat).launch()
```

Now your personal AI assistant will automatically reflect on its answers and improve them when they don't meet quality standards.

---

## Commercial Implications

This pattern—generate a response, evaluate it, and retry with feedback—is a simple yet powerful way to ensure high-quality outputs from LLMs. It can be applied to any customer-facing chatbot, support agent, or content generation system where accuracy and professionalism are critical. By using a second model as a judge, you create a feedback loop that mimics human quality control, all without manual intervention.

> **Note on package discovery:**  
> In this lab we used `pypdf` for PDF reading and `gradio` for UI. When you need new functionality, you can find open-source packages on [PyPI](https://pypi.org) or ask an LLM for recommendations.

---

## Troubleshooting

- **Provider compatibility:** If your main LLM or evaluator is not OpenAI, adjust the client initialization and model names accordingly.
- **History format issues:** Some providers are strict about the message format. Use the cleaning step mentioned earlier.
- **Rate limits / costs:** Be mindful of API costs when running many evaluations. You can adjust the evaluation frequency or use a cheaper model for the judge.

---

This lab demonstrates how to build a robust, self-improving AI assistant using simple LLM calls and a feedback loop—no agentic framework required.
