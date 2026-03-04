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



# Professional You: Building a Deployable AI Assistant with Tools

This lab extends the personal AI assistant by adding **tool use** (function calling) and **Pushover notifications** to alert you when someone interacts with your bot. You'll also deploy the app to Hugging Face Spaces so it's publicly accessible.

## What You'll Build

- A Gradio chat interface that represents you (based on your LinkedIn and summary).
- Two tools:  
  - `record_user_details` – sends a push notification when a user provides their email.  
  - `record_unknown_question` – notifies you when the assistant doesn't know an answer.
- Integration with **Pushover** for mobile push notifications.
- Deployment to Hugging Face Spaces.

---

## Step 1: Set Up Pushover

Pushover sends push notifications to your phone.  
1. Go to [pushover.net](https://pushover.net) and sign up.  
2. After login, click **"Create an Application/API Token"**, give it a name (e.g., "Agents"), and create.  
3. You'll get a **User Key** (top right of home screen) and an **API Token/Key** (inside your new application).  
4. Add them to your `.env` file:

```
PUSHOVER_USER=your_user_key_starts_with_u
PUSHOVER_TOKEN=your_app_token_starts_with_a
```

5. Install the Pushover app on your phone and log in.

---

## Step 2: Imports and Environment

```python
from dotenv import load_dotenv
from openai import OpenAI
import json
import os
import requests
from pypdf import PdfReader
import gradio as gr

load_dotenv(override=True)
openai = OpenAI()

pushover_user = os.getenv("PUSHOVER_USER")
pushover_token = os.getenv("PUSHOVER_TOKEN")
pushover_url = "https://api.pushover.net/1/messages.json"

if pushover_user:
    print(f"Pushover user found and starts with {pushover_user[0]}")
else:
    print("Pushover user not found")

if pushover_token:
    print(f"Pushover token found and starts with {pushover_token[0]}")
else:
    print("Pushover token not found")
```

---

## Step 3: Pushover Helper Function

```python
def push(message):
    print(f"Push: {message}")
    payload = {"user": pushover_user, "token": pushover_token, "message": message}
    requests.post(pushover_url, data=payload)

# Test it
push("HEY!!")
```

---

## Step 4: Define the Tools (Functions)

These are the Python functions that will be called by the LLM via tool calls.

```python
def record_user_details(email, name="Name not provided", notes="not provided"):
    push(f"Recording interest from {name} with email {email} and notes {notes}")
    return {"recorded": "ok"}

def record_unknown_question(question):
    push(f"Recording {question} asked that I couldn't answer")
    return {"recorded": "ok"}
```

---

## Step 5: Tool JSON Schemas

These schemas tell the LLM what arguments each tool expects.

```python
record_user_details_json = {
    "name": "record_user_details",
    "description": "Use this tool to record that a user is interested in being in touch and provided an email address",
    "parameters": {
        "type": "object",
        "properties": {
            "email": {"type": "string", "description": "The email address of this user"},
            "name": {"type": "string", "description": "The user's name, if they provided it"},
            "notes": {"type": "string", "description": "Any additional information about the conversation that's worth recording to give context"}
        },
        "required": ["email"],
        "additionalProperties": False
    }
}

record_unknown_question_json = {
    "name": "record_unknown_question",
    "description": "Always use this tool to record any question that couldn't be answered as you didn't know the answer",
    "parameters": {
        "type": "object",
        "properties": {
            "question": {"type": "string", "description": "The question that couldn't be answered"},
        },
        "required": ["question"],
        "additionalProperties": False
    }
}

tools = [
    {"type": "function", "function": record_user_details_json},
    {"type": "function", "function": record_unknown_question_json}
]
```

---

## Step 6: Tool Call Handler

This function executes the tool calls returned by the LLM. Two versions are shown: one with an explicit `if` statement, and a more dynamic version using `globals()`.

```python
# Dynamic version (preferred)
def handle_tool_calls(tool_calls):
    results = []
    for tool_call in tool_calls:
        tool_name = tool_call.function.name
        arguments = json.loads(tool_call.function.arguments)
        print(f"Tool called: {tool_name}", flush=True)
        tool = globals().get(tool_name)   # fetches the Python function by name
        result = tool(**arguments) if tool else {}
        results.append({
            "role": "tool",
            "content": json.dumps(result),
            "tool_call_id": tool_call.id
        })
    return results
```

---

## Step 7: Load Your Personal Data

Same as before: read your LinkedIn PDF and summary.

```python
reader = PdfReader("me/linkedin.pdf")
linkedin = ""
for page in reader.pages:
    text = page.extract_text()
    if text:
        linkedin += text

with open("me/summary.txt", "r", encoding="utf-8") as f:
    summary = f.read()

name = "Your Name"   # Change to your name
```

---

## Step 8: System Prompt with Tool Instructions

The prompt now tells the LLM when to use the tools.

```python
system_prompt = f"You are acting as {name}. You are answering questions on {name}'s website, \
particularly questions related to {name}'s career, background, skills and experience. \
Your responsibility is to represent {name} for interactions on the website as faithfully as possible. \
You are given a summary of {name}'s background and LinkedIn profile which you can use to answer questions. \
Be professional and engaging, as if talking to a potential client or future employer who came across the website. \
If you don't know the answer to any question, use your record_unknown_question tool to record the question that you couldn't answer, even if it's about something trivial or unrelated to career. \
If the user is engaging in discussion, try to steer them towards getting in touch via email; ask for their email and record it using your record_user_details tool."

system_prompt += f"\n\n## Summary:\n{summary}\n\n## LinkedIn Profile:\n{linkedin}\n\n"
system_prompt += f"With this context, please chat with the user, always staying in character as {name}."
```

---

## Step 9: Chat Function with Tool Loop

The chat function now includes a loop that continues until the LLM produces a final answer (no tool calls).

```python
def chat(message, history):
    messages = [{"role": "system", "content": system_prompt}] + history + [{"role": "user", "content": message}]
    done = False
    while not done:
        response = openai.chat.completions.create(
            model="gpt-4o-mini",
            messages=messages,
            tools=tools
        )
        finish_reason = response.choices[0].finish_reason
        
        if finish_reason == "tool_calls":
            message_obj = response.choices[0].message
            tool_calls = message_obj.tool_calls
            results = handle_tool_calls(tool_calls)
            messages.append(message_obj)
            messages.extend(results)
        else:
            done = True
    return response.choices[0].message.content
```

---

## Step 10: Launch the Gradio App

```python
gr.ChatInterface(chat).launch()
```

Now when someone chats with your bot:
- If they ask something you don't know, you'll get a push notification with the question.
- If they provide an email, you'll get a push with their details.

---

## Step 11: Deploy to Hugging Face Spaces

### Prerequisites
- A [Hugging Face](https://huggingface.co) account.
- A Hugging Face token with **write** permissions (from Settings → Access Tokens).

### Deployment Steps

1. **Install the Hugging Face CLI tool**  
   ```bash
   uv tool install 'huggingface_hub[cli]'
   ```
   (If you don't have `uv`, use `pip install huggingface_hub[cli]`.)

2. **Log in**  
   ```bash
   hf auth login --token YOUR_TOKEN_HERE
   ```
   Verify with `hf auth whoami`.

3. **From the folder containing your `app.py` and `me/` directory**, run:
   ```bash
   gradio deploy
   ```
   - Name the space (e.g., `career_conversation`).
   - Specify `app.py` as the entry point.
   - Choose `cpu-basic` hardware.
   - When asked for secrets, provide:
     - `OPENAI_API_KEY`
     - `PUSHOVER_USER`
     - `PUSHOVER_TOKEN`
   - Say **No** to GitHub Actions.

4. After deployment, your app will be live at:  
   `https://huggingface.co/spaces/YOUR_USERNAME/SPACE_NAME`

### Managing Secrets Later
- Go to your Hugging Face profile → Spaces → your space → Settings → **Variables and secrets** to update keys.

### Troubleshooting
- If the deploy script creates a `README.md` in your local folder, delete it before redeploying (otherwise it skips the setup questions).

---

## Commercial Implications

This pattern—a personal AI assistant with notification tools—can be adapted for business applications like:
- Customer support bots that alert staff when a query can't be answered.
- Lead generation bots that instantly notify sales when a prospect shares contact info.
- Any domain-specific expert bot that needs to escalate or record interactions.

> **Exercise**  
> - Deploy your own version!  
> - Improve the resources: add a knowledge base (RAG) about yourself.  
> - Add more tools: e.g., write to a SQL database, send emails, or integrate a calendar.  
> - Incorporate the evaluator from the previous lab to improve response quality.

---

# The Unreasonable Effectiveness of the Agent Loop

This lab demonstrates a pure **agent loop**: an LLM that uses tools in a loop to accomplish a task, without any predefined workflow. We'll build a simple todo list manager where the LLM plans and executes steps using tools.

## What is an Agent?

Three common definitions:
1. **Sam Altman**: AI systems that can do work for you independently.
2. **Anthropic**: A system in which an LLM controls the workflow.
3. **Emerging definition**: An LLM that runs tools in a loop to achieve a goal.

We'll focus on the third definition.

---

## Step 1: Imports and Setup

```python
from rich.console import Console
from dotenv import load_dotenv
from openai import OpenAI
import json

load_dotenv(override=True)
openai = OpenAI()

def show(text):
    try:
        Console().print(text)
    except Exception:
        print(text)
```

---

## Step 2: Todo List State

We maintain two parallel lists: `todos` (task descriptions) and `completed` (boolean flags).

```python
todos = []
completed = []

def get_todo_report() -> str:
    result = ""
    for index, (todo, done) in enumerate(zip(todos, completed)):
        if done:
            result += f"Todo #{index + 1}: [green][strike]{todo}[/strike][/green]\n"
        else:
            result += f"Todo #{index + 1}: {todo}\n"
    show(result)
    return result
```

---

## Step 3: Tool Functions

```python
def create_todos(descriptions: list[str]) -> str:
    todos.extend(descriptions)
    completed.extend([False] * len(descriptions))
    return get_todo_report()

def mark_complete(index: int, completion_notes: str) -> str:
    if 1 <= index <= len(todos):
        completed[index - 1] = True
    else:
        return "No todo at this index."
    Console().print(completion_notes)
    return get_todo_report()
```

---

## Step 4: Tool JSON Schemas

```python
create_todos_json = {
    "name": "create_todos",
    "description": "Add new todos from a list of descriptions and return the full list",
    "parameters": {
        "type": "object",
        "properties": {
            "descriptions": {
                'type': 'array',
                'items': {'type': 'string'},
                'title': 'Descriptions'
            }
        },
        "required": ["descriptions"],
        "additionalProperties": False
    }
}

mark_complete_json = {
    "name": "mark_complete",
    "description": "Mark complete the todo at the given position (starting from 1) and return the full list",
    "parameters": {
        'properties': {
            'index': {
                'description': 'The 1-based index of the todo to mark as complete',
                'title': 'Index',
                'type': 'integer'
            },
            'completion_notes': {
                'description': 'Notes about how you completed the todo in rich console markup',
                'title': 'Completion Notes',
                'type': 'string'
            }
        },
        'required': ['index', 'completion_notes'],
        'type': 'object',
        'additionalProperties': False
    }
}

tools = [
    {"type": "function", "function": create_todos_json},
    {"type": "function", "function": mark_complete_json}
]
```

---

## Step 5: Tool Call Handler

Same dynamic handler as before.

```python
def handle_tool_calls(tool_calls):
    results = []
    for tool_call in tool_calls:
        tool_name = tool_call.function.name
        arguments = json.loads(tool_call.function.arguments)
        tool = globals().get(tool_name)
        result = tool(**arguments) if tool else {}
        results.append({
            "role": "tool",
            "content": json.dumps(result),
            "tool_call_id": tool_call.id
        })
    return results
```

---

## Step 6: The Agent Loop

The `loop` function takes a list of messages and repeatedly calls the LLM, handling any tool calls until a final answer is produced.

```python
def loop(messages):
    done = False
    while not done:
        response = openai.chat.completions.create(
            model="gpt-4o-mini",  # use a model with tool support
            messages=messages,
            tools=tools
        )
        finish_reason = response.choices[0].finish_reason
        if finish_reason == "tool_calls":
            message_obj = response.choices[0].message
            tool_calls = message_obj.tool_calls
            results = handle_tool_calls(tool_calls)
            messages.append(message_obj)
            messages.extend(results)
        else:
            done = True
    show(response.choices[0].message.content)
```

---

## Step 7: Run the Agent on a Problem

We give the agent a system prompt that instructs it to plan using todos and execute steps. Then we provide a user question.

```python
system_message = """
You are given a problem to solve, by using your todo tools to plan a list of steps, then carrying out each step in turn.
Now use the todo list tools, create a plan, carry out the steps, and reply with the solution.
If any quantity isn't provided in the question, then include a step to come up with a reasonable estimate.
Provide your solution in Rich console markup without code blocks.
Do not ask the user questions or clarification; respond only with the answer after using your tools.
"""

user_message = """
A train leaves Boston at 2:00 pm traveling 60 mph.
Another train leaves New York at 3:00 pm traveling 80 mph toward Boston.
When do they meet?
"""

messages = [
    {"role": "system", "content": system_message},
    {"role": "user", "content": user_message}
]

todos, completed = [], []   # reset state
loop(messages)
```

The agent will:
1. Create a todo list of steps (e.g., find distance, calculate relative speed, compute time).
2. Mark each step complete as it works through them.
3. Finally output the answer with rich formatting.

---

## Why This Matters

This simple loop demonstrates the core of agentic behavior:  
- The LLM **decides** which tools to call and in what order.  
- It maintains its own plan via the todo list.  
- It iterates until the task is done.

This pattern can be extended to any set of tools—APIs, databases, calculators, etc.—making it a foundational building block for complex AI agents.

> **Exercise**  
> Build your own agent loop from scratch! Create a new notebook and implement a loop with your own tools (e.g., a calculator, a web search simulator). It's a great way to internalize the concept.
- **Rate limits / costs:** Be mindful of API costs when running many evaluations. You can adjust the evaluation frequency or use a cheaper model for the judge.

---

This lab demonstrates how to build a robust, self-improving AI assistant using simple LLM calls and a feedback loop—no agentic framework required.
