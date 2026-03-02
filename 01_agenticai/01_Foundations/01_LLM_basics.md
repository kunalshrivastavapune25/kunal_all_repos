# Large Language Models (LLMs)

## Definition
A Large Language Model (LLM) is an AI model trained on massive text data to understand and generate human-like language.

## Popular Examples
- GPT-5 (OpenAI)
- LLaMA (Meta)
- Claude (Anthropic)
- Gemini (Google DeepMind)
- Mistral (Mistral AI)

## Important Concepts

### Parameters
Weights learned during training.
More parameters → More capability (usually).

### Context Window
Maximum number of tokens (input + output) the model can process at once.

### Non-Deterministic Nature
Same question may produce different answers.
Because output depends on probability.

---

## Using LLMs

### OpenAI
Set API key:
OPENAI_API_KEY="sk-..."

### Google Gemini
GOOGLE_API_KEY="AI-..."

### Ollama (Local LLM)
Install from: https://ollama.com

Pull model:
ollama pull llama3.2:latest

Run model:
ollama run llama3.2:latest

---

## Sample .env file

OPENAI_API_KEY="sk-..."
GOOGLE_API_KEY="AI-..."
