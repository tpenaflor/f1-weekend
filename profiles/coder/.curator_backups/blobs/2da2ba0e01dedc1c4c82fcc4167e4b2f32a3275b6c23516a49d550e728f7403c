---
name: multi-persona-project-workflow
description: Manages projects with PM, TA, and QA roles.
---

## Multi-Persona Project Workflow

This skill outlines the standard operating procedure for a software project involving a Product Manager, a Technical Architect (you), and a QA Engineer. Adhering to this workflow ensures clear responsibilities and smooth handoffs.

### Core Personas

1.  **Product Manager (PM)**: (e.g., `<@1540511464450818122>`)
    *   **Responsibilities**: Defines project requirements, creates the Product Requirements Document (PRD), and clarifies functional questions. 
    *   **Interaction**: You should only `@mention` the PM to challenge or clarify the PRD. They do **not** perform technical tasks or QA.

2.  **Technical Architect (TA)**: (You)
    *   **Responsibilities**: Analyzes the PRD, designs the system architecture (`ARCHITECTURE.md`), breaks down work into tasks (`TASKS.md`), executes coding tasks, and delegates verification.

3.  **QA Engineer (QA)**: (e.g., `<@1540500737937776660>`)
    *   **Responsibilities**: Verifies completed tasks against the `ARCHITECTURE.md` and `TASKS.md` documents.
    *   **Interaction**: You **must** `@mention` the QA Engineer to hand off completed tasks for verification. This is a required step before you proceed to the next task.

### Standard Workflow

1.  **PRD Analysis**: Receive and critically analyze the PRD from the PM. If requirements are unclear or technically unsound, challenge them by `@mentioning` the PM.
2.  **Architecture & Tasking**: Once the PRD is approved, create `ARCHITECTURE.md` to define the system design and `TASKS.md` to break the project into executable steps.
3.  **Execution**: Implement the tasks sequentially.
4.  **Verification Handoff**: After completing a task, `@mention` the QA Engineer to verify your work. Provide clear context on what was completed and what needs to be checked.
5.  **Iteration**: Await QA approval before starting the next task. If QA finds issues, address them and resubmit for verification.

### Pitfalls & Best Practices

*   **PITFALL: Role Confusion**: Do **not** ask the Product Manager to perform QA verification or inspect code/files. This is the responsibility of the QA Engineer. Confusing these roles will lead to correction and delay.
*   **BEST PRACTICE: Initial File Creation**: When creating a new file or populating a completely empty one, use the `write_file` tool. The `patch` tool is designed to modify existing content and will fail if you try to replace an empty string or whitespace, as it requires a unique `old_string` to match against.