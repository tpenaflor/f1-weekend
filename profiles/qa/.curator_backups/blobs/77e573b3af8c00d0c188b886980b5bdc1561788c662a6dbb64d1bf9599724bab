---
name: multi-agent-file-sync
description: Troubleshoot stale file reads in multi-agent environments.
author: Hermes QA
tags: [file-system, troubleshooting, multi-agent, caching]
---

## Multi-Agent File Synchronization Troubleshooting

This skill provides guidance on resolving file content inconsistencies that can occur in multi-agent workflows, particularly when file reading tools report stale data despite successful writes.

### Trigger Conditions
Use this skill when:
- A coder reports successfully writing to a file, but subsequent reads (especially by another agent or a different tool instance) show old or unchanged content.
- `read_file` tool repeatedly reports "file unchanged" even after a `write_file` operation.
- There is suspicion of caching issues or delayed synchronization in the file system view across different agents.

### Workflow
1. **Confirm Discrepancy:** Verify that the reported file content differs from the expected content after a write operation.
2. **Bypass Caching with `terminal cat`:** If `read_file` is reporting stale content, use the `terminal` tool to directly `cat` the file's content. This bypasses potential caching mechanisms and provides the most up-to-date view of the file on disk.
   ```
   terminal(command="cat <file_path>")
   ```
3. **Communicate Findings:** Inform other agents (e.g., Coder, PM) about the discrepancy and the successful retrieval of the correct content using the workaround.
4. **Proceed with Verification:** Use the content obtained via `terminal cat` for verification or further processing.

### Pitfalls
- Relying solely on `read_file` when inconsistencies are suspected can lead to continuous loops and misdiagnosis.
- Repeatedly stating the same waiting message without seeking alternative ways to unblock or acknowledging external interventions from other agents (e.g., PM explicitly prompting the coder).

### Communication Best Practices in Multi-Agent Loops
- If a waiting state persists and seems to form a loop, proactively look for signals of intervention from other agents (e.g., Product Manager explicitly prompting the Coder).
- When a loop is detected, and before repeating a waiting message, consider if a direct prompt to the blocking agent, or an alternative strategy (like the `terminal cat` workaround), is more appropriate.

### Related Information
- For an example of this issue and its resolution, refer to `references/stale-file-read-workaround.md`.
