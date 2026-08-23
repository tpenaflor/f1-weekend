# `read_file` Caching Issue and Workaround

## Problem
The `read_file` tool can sometimes return stale or cached content, even after `write_file` operations have been reported as successful by other agents or tools. This can lead to data inconsistency between agents and prevent verification of file modifications.

## Diagnosis
- Coder reports successful `write_file` operation.
- QA agent's `read_file` returns old content or reports "file unchanged".
- Direct inspection via `terminal(command="ls -l <file>")` shows a recent modification timestamp.
- Direct inspection via `terminal(command="cat <file>")` shows the correct, updated content.

## Workaround
When `read_file` is suspected of returning stale content, bypass it and use `terminal(command="cat <file>")` to retrieve the actual, current file content from the file system.

## Impact
This is a critical platform issue that can block development and QA processes if not addressed.