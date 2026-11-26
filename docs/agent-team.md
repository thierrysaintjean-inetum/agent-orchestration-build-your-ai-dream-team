# Mona's Project Pulse Agent Team

## Orchestrator

- **Model:** Claude Opus 5.5
- **Definition:** `.github/agents/orchestrator.agent.md`
- **Responsibility:** Coordinates the team from GitHub Copilot CLI. The Orchestrator turns Mona's dashboard request into phases, delegates work to the specialist agents with explicit file scopes, manages dependencies, and verifies that the integrated result is coherent.

## Planner

- **Model:** Claude Opus 5.5
- **Definition:** `.github/agents/planner.agent.md`
- **Responsibility:** Researches the repository and Project Pulse requirements, then produces an ordered implementation plan with file assignments, dependencies, parallel work, sequential work, edge cases, risks, and validation expectations.

## Coder

- **Model:** GPT-5.5
- **Definition:** `.github/agents/coder.agent.md`
- **Responsibility:** Implements the assigned static application files with clear, consistent, and testable code. For Project Pulse, the Coder creates the dashboard implementation and the support configuration needed to run it.

## Designer

- **Model:** Gemini 3.7 Flash
- **Definition:** `.github/agents/designer.agent.md`
- **Responsibility:** Defines the dashboard experience through usability, accessibility, information hierarchy, responsive behavior, and visual clarity. The Designer ensures Project Pulse uses a polished layout with project cards, status badges, readable spacing, and clear priority treatment.

## How the team works together

The Orchestrator starts by asking the Planner to inspect `.github/agents/` and the repository, producing a plan for Mona's Project Pulse dashboard. The Orchestrator then separates the plan into phases with non-overlapping file scopes. The Designer owns the visual and interaction direction, while the Coder implements the assigned HTML, CSS, data, and launch configuration. Parallel work is limited to independent file scopes; dependent work runs in sequence, and the Orchestrator verifies that the final pieces work together.
