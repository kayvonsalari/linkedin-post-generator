# LinkedIn Post Generator

Multi-agent pipeline that generates, refines, and evaluates LinkedIn posts through a loop of specialised agents — producing audience-aware, consistently high-quality content.

---

## Why Multi-Agent?

A single LLM prompt producing a LinkedIn post tends to be generic — it has no mechanism for self-correction, no separation between research and writing, and no independent quality gate. This system divides the task into four agents with distinct responsibilities, making the output easier to debug, tune per agent, and extend with new evaluation criteria.

## Architecture

```
Start → Supervisor → Researcher → Writer → Critic → Supervisor → END
              ↑                                           ↓
              └───────── (loop until approved) ───────────┘
```

**Supervisor** orchestrates the workflow — routes tasks between agents, decides when to iterate, and halts when the Critic approves.

**Researcher** gathers background material, facts, and references relevant to the post topic. Can be extended to query external tools or knowledge bases.

**Writer** drafts the LinkedIn post using the topic and research output. Prompt tuned for professional tone, platform conventions, and audience specificity.

**Critic** evaluates the draft against a scoring rubric — engagement potential, factual accuracy, tone, and call-to-action clarity. Either approves or requests a rewrite with specific feedback.

## State

A shared `StateGraph` holds the current task, intermediate results, and agent flow — persisting across iterations without re-running completed phases.

## Tech Stack

- **Orchestration**: LangGraph
- **LLM**: OpenAI
- **Language**: Python

## Key Design Decisions

**Critic-gated output**: The pipeline never exits before the Critic approves. This prevents the system from surfacing low-quality first drafts and enforces a quality floor regardless of the topic.

**Researcher as a separate agent**: Separating research from writing means the Writer receives structured, pre-validated background material rather than relying on the LLM's parametric knowledge alone. Reduces hallucination risk on factual claims and makes sourcing transparent.

**Supervisor as the control plane**: The Supervisor holds routing logic separately from the agents themselves. Adding a new agent (e.g. a Compliance Checker for regulated industries) requires only a routing rule change, not modifications to Writer or Critic prompts.

---

*Part of Kayvon Salari's agentic AI portfolio. [kayvonsalari.github.io](https://kayvonsalari.github.io)*
