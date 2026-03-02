---

# Agentic AI: Key Ideas

| Aspect | Meaning (Short) | Why It Matters |
|--------|------------|------------|
Tools and Actions APIs | code, DB, automation | Turns decisions into real outcomes | 
Memory | Short + long-term context | Continuity and multi-step tasks | 
RAG | Retrieve facts before answering | Accurate, up-to-date results | 
Reasoning and Planning | Decompose and plan steps | Solves complex problems reliably | 
Human-in-the-Loop | Approvals and oversight | Safety and governance | 
Explainability | Traces and reasoning visibility | Trust and debuggability | 
Observability | Logs, metrics, traces | Debugging and optimization | 
MCP | Standard tool/data access | Cross-platform interoperability | 
Multi-Agent | Specialized agents collaborate | Speed, scale, parallelism | 
Safety and Guardrails | Policies and validation | Prevents harmful actions | 
Feedback Loops | Retry and self-correction | Better accuracy | 
Learning and Adaptation | Update memory/strategy | Personalization and efficiency | 
Deployment | Pipelines, APIs, containers | Production readiness | APIs, Docker, AWS, CI/CD. | 

---


# Agentic AI Frameworks/Technologies
| Level | Tool / Framework | Role  Why it fits here | 
|--------|------------|------------|
1 | OpenAI SDK | Base API/SDK | Lowest level – just an SDK to call LLMs, embeddings, etc. We build everything else on top | 
2 | OpenAI Agents SDK | Structured agent building | More structured agents – adds abstractions for tools,memory, structured responses, making agent creation easier | 
3 | LangGraph | Workflow + Agent graphs | Lets us compose agents/workflows as state machines/graphs.Good for predictable flows or multi-agent orchestration | 
4 | CrewAI | Multi-agent orchestration | Specializes in multi-agent “crew” collaboration, roles, and task distribution, built for autonomy | 
5 | AutoGen | Multi-agent conversationframework | Similar to CrewAI, but focuses heavily on conversational agentto-agent loops (like negotiation, reasoning) | 
6 | MCP (Model ContextProtocol) | Standard protocol for agenttool clientcommunication | Sits at the top as infrastructure – not an agent frameworkitself, but a protocol that makes all the above interoperable | 
7 | No Code-Low Code | Create agentic AIapplications without writingcode | Uses drag-and-drop features with a rich set of connectors tocreate powerful applications(Examples: n8n, OpenAI AgentBuilder) | 







