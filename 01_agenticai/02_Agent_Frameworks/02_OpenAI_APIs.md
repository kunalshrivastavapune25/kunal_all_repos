# Agentic AI Automation: A Practical Guide

This repository demonstrates three common approaches to building agentic AI automations using LLMs. It includes practical code examples for interacting with multiple LLM providers, generating questions, and comparing model responses.

## Three Approaches to Agentic AI

1. **Direct LLM API calls** – using OpenAI’s ChatCompletion directly.
2. **Frameworks with custom tools** – e.g., LangGraph, CrewAI, AutoGen.
3. **Frameworks with MCPs (Model Context Protocols)** – using function tools created by others.

Additional concepts like RAG (Retrieval-Augmented Generation) and vector databases are also relevant but not covered in these examples.

---

## Setup

### 1. Install required packages

```bash
pip install openai anthropic python-dotenv IPython
```

*Optional:* For local models via Ollama, install [Ollama](https://ollama.com/) and pull a model, e.g.:

```bash
ollama pull llama3.2
```

### 2. Set up environment variables

Create a `.env` file with your API keys (only those you plan to use):

```
OPENAI_API_KEY=your-key
ANTHROPIC_API_KEY=your-key
GOOGLE_API_KEY=your-key
DEEPSEEK_API_KEY=your-key
GROQ_API_KEY=your-key
```

---

## Use Case 1: Simple Calculation with OpenAI

```python
from dotenv import load_dotenv
load_dotenv(override=True)
import os
from openai import OpenAI

# Verify key is loaded
openai_api_key = os.getenv('OPENAI_API_KEY')
if openai_api_key:
    print(f"OpenAI API Key exists and begins {openai_api_key[:8]}")
else:
    print("OpenAI API Key not set")

openai = OpenAI()

messages = [{"role": "user", "content": "What is 2+2?"}]
response = openai.chat.completions.create(
    model="gpt-4.1-nano",          # Replace with a valid model
    messages=messages
)
print(response.choices[0].message.content)
```

---

## Use Case 2: Generate a Tough Question and Answer It

```python
# Step 1: Ask the LLM to generate a challenging IQ question
question_prompt = "Please propose a hard, challenging question to assess someone's IQ. Respond only with the question."
messages = [{"role": "user", "content": question_prompt}]
response = openai.chat.completions.create(
    model="gpt-4.1-mini",
    messages=messages
)
question = response.choices[0].message.content
# Step 2: Answer the generated question
messages = [{"role": "user", "content": question}]
response = openai.chat.completions.create(
    model="gpt-4.1-mini",
    messages=messages
)
answer = response.choices[0].message.content
display(Markdown(answer))
```

---

## Use Case 3: Compare and Rank Responses from Multiple LLMs

This example sends the same challenging question to several models and then uses a judge model to rank the answers.

### Step 1: Generate a question (using OpenAI)

```python
# Generate a challenging question
request = "Please come up with a challenging, nuanced question that I can ask a number of LLMs to evaluate their intelligence. Answer only with the question, no explanation."
messages = [{"role": "user", "content": request}]
response = openai.chat.completions.create(
    model="gpt-4.1-mini",
    messages=messages
)
question = response.choices[0].message.content
```
### Step 2: Collect answers from various models

```python
competitors = []
answers = []

messages = [{"role": "user", "content": question}]

# OpenAI
model_name = "gpt-4.1-nano"  # or "gpt-4.1-mini" for faster response
response = openai.chat.completions.create(model=model_name, messages=messages)
answer = response.choices[0].message.content
display(Markdown(answer))
competitors.append(model_name)
answers.append(answer)

# Anthropic Claude
model_name = "claude-3-sonnet-20240229"  # example model
claude = Anthropic()
response = claude.messages.create(model=model_name, messages=messages, max_tokens=1000)
answer = response.content[0].text
display(Markdown(answer))
competitors.append(model_name)
answers.append(answer)

# Google Gemini (using OpenAI-compatible endpoint)
gemini = OpenAI(api_key=google_api_key, base_url="https://generativelanguage.googleapis.com/v1beta/openai/")
model_name = "gemini-1.5-flash"
response = gemini.chat.completions.create(model=model_name, messages=messages)
answer = response.choices[0].message.content
display(Markdown(answer))
competitors.append(model_name)
answers.append(answer)

# DeepSeek
deepseek = OpenAI(api_key=deepseek_api_key, base_url="https://api.deepseek.com")
model_name = "deepseek-chat"
response = deepseek.chat.completions.create(model=model_name, messages=messages)
answer = response.choices[0].message.content
display(Markdown(answer))
competitors.append(model_name)
answers.append(answer)

# Groq (running open-source models)
groq = OpenAI(api_key=groq_api_key, base_url="https://api.groq.com/openai/v1")
model_name = "llama-3.3-70b-versatile"  # example Groq model
response = groq.chat.completions.create(model=model_name, messages=messages)
answer = response.choices[0].message.content
display(Markdown(answer))
competitors.append(model_name)
answers.append(answer)

# Local Ollama (if running)
ollama = OpenAI(base_url='http://localhost:11434/v1', api_key='ollama')
model_name = "llama3.2"
response = ollama.chat.completions.create(model=model_name, messages=messages)
answer = response.choices[0].message.content
display(Markdown(answer))
competitors.append(model_name)
answers.append(answer)
```

### Step 3: Prepare a judge prompt and rank answers

```python
# Combine all answers into a single text
together = ""
for index, answer in enumerate(answers):
    together += f"# Response from competitor {index+1}\n\n"
    together += answer + "\n\n"

judge_prompt = f"""You are judging a competition between {len(competitors)} competitors.
Each model has been given this question:
{question}
Your job is to evaluate each response for clarity and strength of argument, and rank them in order of best to worst.
Respond with JSON, and only JSON, with the following format:
{{"results": ["best competitor number", "second best competitor number", "third best competitor number", ...]}}
Here are the responses from each competitor:
{together}
Now respond with the JSON with the ranked order of the competitors, nothing else. Do not include markdown formatting or code blocks."""

judge_messages = [{"role": "user", "content": judge_prompt}]
response = openai.chat.completions.create(
    model="gpt-4.1-mini",
    messages=judge_messages,
)
results = response.choices[0].message.content
print(results)

# Parse JSON and display ranks
results_dict = json.loads(results)
ranks = results_dict["results"]
for index, result in enumerate(ranks):
    competitor = competitors[int(result)-1]
    print(f"Rank {index+1}: {competitor}")
```

---







# OpenAI APIs & SDKs Demo

A concise comparison of three ways to build with OpenAI: **Chat Completions API**, **Responses API**, and **Agents SDK** – all demonstrated through a **DevOps assistant** that analyses AWS diagrams and generates Terraform code.
## What’s What?

### Chat Completions API
- **What**: The classic `/v1/chat/completions` endpoint – stateless, client-managed history.
- **When**: Simple tasks, full control, legacy apps.
- **Capabilities**: Text/image inputs, function calling, streaming.
- **Limits**: No built‑in tools, no server‑side state.

### Responses API
- **What**: New primary endpoint (replaces Assistants API). Supports server‑side state and hosted tools.
- **When**: New projects needing code interpreter, file search, web search.
- **Capabilities**: Built‑in tools, conversation objects (`previous_response_id`), richer output.
- **Limits**: No multi‑agent orchestration; custom tools still client‑side.

### Agents SDK
- **What**: Open‑source framework for multi‑agent workflows (Python/TypeScript).
- **When**: Complex orchestration, handoffs, guardrails, tracing.
- **Capabilities**: Agents, handoffs, guardrails, automatic tracing, real‑time voice.
- **Limits**: Overhead for simple tasks; runs on your server.

---

## Real‑World Use Case: DevOps Assistant

The assistant analyses an uploaded AWS diagram and outputs Terraform code. Here’s how each approach handles it:

| Aspect              | Chat Completions                          | Responses API                            | Agents SDK                                          |
| ------------------- | ----------------------------------------- | ---------------------------------------- | --------------------------------------------------- |
| **Image input**     | Message with image_url                    | Same                                     | Same                                                |
| **Diagram analysis**| Model describes; custom tool for validation| Use web search / code interpreter built‑in| Dedicated analyser agent with tools                 |
| **Terraform gen**   | Model outputs HCL; manual formatting       | Can use code interpreter to validate      | Terraform agent hands off to security reviewer       |
| **Multi‑turn**      | Client stores history                      | Use `previous_response_id`                | SDK manages sessions                                 |
| **Orchestration**   | Manual loops                               | Manual API chaining                       | Handoffs, parallel agents, guardrails               |

---

## Feature Comparison

| Feature                | Chat Completions | Responses API      | Agents SDK               |
| ---------------------- | ---------------- | ------------------ | ------------------------ |
| State management       | Client-side      | Server-side (opt)  | Framework-managed        |
| Built‑in tools         | No               | Yes                | Yes (via underlying API) |
| Custom tools           | Yes (client exec)| Yes (client exec)  | Yes (integrated)         |
| Multi‑agent orchestration| No             | No                 | **First‑class**          |
| Guardrails             | No               | No                 | Built‑in                 |
| Tracing                | No               | No                 | Automatic                |
| Human‑in‑the‑loop      | No               | No                 | Yes                      |
| Realtime voice         | No               | No                 | Yes (`RealtimeAgent`)    |

---

## Sample Code (TypeScript)

### Chat Completions – Basic + Tool
```ts
import OpenAI from 'openai';
const openai = new OpenAI();

// Single turn with image
const res = await openai.chat.completions.create({
  model: 'gpt-4o',
  messages: [{ role: 'user', content: [{ type: 'image_url', image_url: { url: 'diagram.png' } }] }]
});

// With tool definition
const toolRes = await openai.chat.completions.create({
  model: 'gpt-4o',
  messages: [...],
  tools: [{ type: 'function', function: { name: 'get_instance_types', ... } }]
});
```

### Responses API – Single + Multi‑turn
```ts
// Single
const res1 = await openai.responses.create({
  model: 'gpt-4o',
  input: [{ role: 'user', content: [{ type: 'input_image', image_url: 'diagram.png' }] }]
});

// Multi‑turn using previous_response_id
const res2 = await openai.responses.create({
  model: 'gpt-4o',
  previous_response_id: res1.id,
  input: 'Now generate Terraform.'
});
```

### Agents SDK – Multi‑Agent Handoff
```ts
import { Agent, run } from '@openai/agents';

const analyser = new Agent({ name: 'Analyser', instructions: 'List resources.' });
const terraform = new Agent({ name: 'TerraformWriter', instructions: 'Write HCL.' });
const security = new Agent({ name: 'SecurityReviewer', instructions: 'Check compliance.' });

const orchestrator = new Agent({
  name: 'Orchestrator',
  instructions: 'Analyse, then write Terraform, then review.',
  handoffs: [analyser, terraform, security]
});

const result = await run(orchestrator, { input: [{ type: 'image_url', image_url: 'diagram.png' }] });
```

---

## Architecture Explained

- **Chat Completions**: All state + logic on client. You store history, execute tools, and manage loops.
- **Responses API**: State optionally on server (`conversation` or `previous_response_id`). Built‑in tools run on OpenAI; custom tools still client‑side.
- **Agents SDK**: Framework runs the agent loop on your server. Manages handoffs, sessions, and tracing automatically.

---

## Best Practices

### When to Choose
- **Chat Completions**: Simple, existing apps, full control.
- **Responses API**: New projects needing hosted tools or server‑side state.
- **Agents SDK**: Complex multi‑agent workflows, safety (guardrails), observability.

### Migration: Chat Completions → Responses API
- Replace endpoint.
- Use `previous_response_id` for multi‑turn.
- Add built‑in tools via `tools` array.
- Streaming events change (e.g., `response.output_text.delta`).

### Production Considerations
- Implement retries with backoff.
- Monitor rate limits.
- Use Agents SDK tracing in dev, disable in prod if privacy matters.
- Guardrails prevent prompt injection.
- Track built‑in tool costs.

---

Each folder (`/chat-completions`, `/responses-api`, `/agents-sdk`) contains full runnable examples.


