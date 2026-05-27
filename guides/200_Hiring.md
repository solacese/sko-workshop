# Stage 1: Hiring — Define the Role

<img src="img/hiring.png" alt="Hiring" width="400"/>

Before writing a single line of agent configuration, define what the agent is for. This means establishing its responsibilities, scope of authority, and what success looks like in measurable terms. This stage covers authoring the system prompt that sets the agent's role, skills, behavioral parameters, and guardrails: explicit constraints on what the agent can and cannot do. Treat this as an organizational decision, not just a technical one: an agent without a clear role definition behaves like an employee without a job description, duplicating work, overstepping boundaries, or stalling on decisions that should be automatic.

---

## Solace Agent Mesh Features

- **SAM Builder Agent** — A conversational, canvas-backed UI agent that guides users through requirements gathering, architecture design, YAML config generation, and deployment — the primary no-code authoring experience for defining agent roles.
- **AI Assistant** — An LLM-powered endpoint that generates a complete agent config from a natural-language description; the fast path for non-technical users to go from idea to deployable agent.
- **Agent (`kind: agent`)** — The core platform resource that binds a system prompt, skills, toolsets, and model into a deployable unit; the formal definition of an agent's role and boundaries.
- **System Prompt / Instruction** — The per-agent natural-language definition of role, expertise, scope constraints, and behavioral guardrails; validated at 100–10,000 characters.
- **Skills (`kind: skill`)** — Loadable knowledge bundles (SKILL.md + references) that agents pull on demand; separates durable domain knowledge from the base instruction to keep context lean and role-focused.
- **Agent Card Skills** — Public capability advertisements the agent broadcasts on the broker via A2A; used by orchestrators and peer agents to decide whether to delegate a task to this agent.
- **ValidateComponentConfig Tool** — Validates every generated agent config against the JSON Schema before saving, with structured per-path error messages enforcing correctness of `systemPrompt`, `skills`, and `toolsets`.
- **`sam config apply` / Declarative Config CLI** — GitOps-style CLI (`plan` / `apply` / `pull`) for authoring and version-controlling all agent resources as YAML; plan output shows a structured diff before anything changes.
