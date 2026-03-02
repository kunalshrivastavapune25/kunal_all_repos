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
