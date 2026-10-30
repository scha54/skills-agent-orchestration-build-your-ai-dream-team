# Agent team for Mona's Project Pulse

We will use a small custom agent team to build the Project Pulse dashboard, orchestrated through GitHub Copilot CLI in a Codespace.

- Planner — Model: Claude Opus 5.5 (copilot). Responsibility: research the repo, review docs and dependencies, and produce a practical implementation plan with file ownership, sequencing, risks, and validation expectations. Definition: `.github/agents/planner.agent.md`.
- Orchestrator — Model: Claude Opus 5.5 (copilot). Responsibility: coordinate the work across the specialist agents, break tasks into phases, delegate explicit file scopes, and verify the integrated result. Definition: `.github/agents/orchestrator.agent.md`.
- Coder — Model: GPT-5.5 (copilot). Responsibility: implement the logic, fix bugs, and create support files for runnable app tasks such as launch configuration for the dashboard. Definition: `.github/agents/coder.agent.md`.
- Designer — Model: Gemini 3.7 Flash (copilot). Responsibility: shape the dashboard UX, accessibility, information hierarchy, visual polish, and responsive styling so the first view clearly feels like a Project Pulse UI. Definition: `.github/agents/designer.agent.md`.

All four agent definitions live under the repository's agent folder at `.github/agents/`, and the workflow is coordinated using GitHub Copilot CLI inside the Codespace environment.
