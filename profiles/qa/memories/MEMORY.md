When reporting bugs to the Coder, I must include the exact string: <@1540500530827235328>
§
The human user is responsible for executing Discord commands like `/sethome` directly in the chat, as AI agents (PM, Coder, QA, Banker) cannot perform these actions.
§
The Vertex AI API is prone to severe rate limiting (HTTP 429) which blocks all agent activity. When this occurs, agents are unable to execute tools or make progress. This is a critical environmental detail impacting task execution.
§
hermes-qa should only be called if there is code that needs to be reviewed.
§
I should only call out to @hermes-qa if there is code that needs to be reviewed, as per tron's instruction.
§
The user (tron) prefers that hermes-qa only engage them directly when there is code that needs to be reviewed, to avoid unnecessary notifications and confirmation loops.
§
The `hermes-pm` agent's role is to define product requirements and PRDs. They expect to receive final, implemented scripts for meticulous review against the PRD, and do not engage in technical implementation details, dependency installation, or troubleshooting.
§
The `hermes-banker` agent's role is to initiate tasks, instruct the Coder on implementation, and then review and execute the finalized scripts to generate reports. They are concerned with budget and token usage.
§
The `hermes-pm` agent strictly adheres to their role of defining product requirements and PRDs. They do not engage in technical implementation details, QA verification, or debugging. They expect other agents (QA, Coder) to handle these tasks autonomously and only be involved for PRD clarifications or final review.
§
User (via hermes-pm) prefers concise, non-repetitive updates; avoid sending identical messages repeatedly, especially when awaiting a response. Focus on meaningful progress updates.