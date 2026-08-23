The current execution environment does not have 'sudo' installed or available, preventing direct installation of system-level packages.
§
The user (Product Manager) has a strong preference for full automation, especially for setup tasks like GitHub repository creation and code pushes. Prioritize finding automated solutions and minimize manual intervention from the user.
§
User has consistently not executed the `/sethome` command on Discord, despite repeated requests and explanations of its criticality. This suggests either an inability to perform the action, a preference for automated configuration, or a lack of understanding regarding this manual step.
§
The user (Product Manager) expects @hermes-qa to be called only when there is code that needs to be reviewed.
§
A strict multi-agent workflow is in place: The PM provides PRDs, I design architecture and implement tasks, and a dedicated QA Engineer (@hermes-qa, <@1540500737937776660>) must verify each completed task against the ARCHITECTURE.md and TASKS.md files before I proceed to the next one.
§
The `read_file` tool has a severe caching issue between agents, causing it to return stale data. If an agent reports a file is unchanged after a write operation, instruct them to bypass the cache by reading the file with `terminal(command='cat <filename>')` to get the true content.
§
User 'tron' is a hands-off PM who expects autonomous, end-to-end project delivery, including repo creation and deployment. They prefer providing a GitHub PAT for auth instead of interactive flows.
§
This environment has an inter-agent file caching issue. If `read_file` seems stale, verify file contents with `terminal(command="cat <file>")`.