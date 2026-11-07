# Triage Labels

This repository uses a set of canonical triage labels to manage the lifecycle of issues and tasks. These labels help coordinate work between human contributors and AI agents.

## Canonical Labels

| Label             | Meaning                                                                                                           | Action Required                                                                               |
| :---------------- | :---------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------- |
| `needs-triage`    | The issue has been reported but not yet analyzed or categorized.                                                  | A human or agent needs to review the issue, define requirements, and assign it to a workflow. |
| `needs-info`      | More information is required from the reporter or another stakeholder before work can proceed.                    | Reach out to the requester for clarification or missing details.                              |
| `ready-for-agent` | The issue is well-defined and ready for an AI agent to implement.                                                 | An agent can claim this task and begin the implementation process.                            |
| `ready-for-human` | The issue requires human intervention (e.g., complex architectural decision, manual testing, or security review). | A human developer needs to take over the task.                                                |
| `wontfix`         | The issue is deemed not worth fixing, a duplicate, or intended behavior.                                          | No further action is required; the issue can be closed.                                       |

## Workflow Integration

These labels are used by triage skills to route tasks efficiently:

- **Triage Process**: Issues typically start at `needs-triage` $\rightarrow$ move to `needs-info` (if unclear) $\rightarrow$ then to either `ready-for-agent` or `ready-for-human`.
- **Resolution**: Once a task is completed and verified, the triage label is typically removed in favor of a status like `resolved` or simply closing the issue.
