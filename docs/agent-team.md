# Project Pulse agent team

Mona's Project Pulse dashboard will be built by a coordinated team of custom
agents defined in `.github/agents/`.

| Agent        | Model           | Source file                            | Responsibility                                                                                                                                                                            |
| ------------ | --------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Orchestrator | Claude Opus 4.7 | `.github/agents/orchestrator.agent.md` | Breaks the dashboard request into phases, delegates scoped work to the specialists, coordinates dependencies, verifies the integrated result, and reports the outcome.                    |
| Planner      | Claude Opus 4.7 | `.github/agents/planner.agent.md`      | Researches the repository and requirements, then creates the implementation plan with file ownership, dependencies, edge cases, parallel work, and validation expectations.               |
| Designer     | Gemini 3.1 Pro  | `.github/agents/designer.agent.md`     | Defines the dashboard's user experience, accessibility, information hierarchy, responsive layout, and polished visual direction for project cards, status badges, and priority treatment. |
| Coder        | GPT-5.5         | `.github/agents/coder.agent.md`        | Implements the assigned dashboard code with clear, testable behavior; this includes the static app files and, when assigned, the Project Pulse launch configuration.                      |

## How the team builds Project Pulse

The Orchestrator first asks the Planner for an implementation strategy and uses
that plan to assign non-overlapping file scopes. The Designer shapes the
dashboard experience and visual system while the Coder implements the assigned
static app files: `app/index.html`, `app/styles.css`, and
`app/project-data.json`. The Coder can also create `.vscode/launch.json` so
the **Run Project Pulse Dashboard** configuration opens `app/index.html`.
Finally, the Orchestrator checks that the work fits together and communicates
the completed dashboard handoff.

Orchestrator, Planner, Coder, and Designer.
The model assigned to each agent.
The responsibility of each agent.
The .github/agents/ file for each agent.
How the team will work together to build Project Pulse.
