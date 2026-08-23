---
name: github-deployment-pitfalls
description: "Fixes common GitHub deployment errors in this environment."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [GitHub, Deployment, Git, Troubleshooting, Workarounds]
    related_skills: [github-repo-management, github-auth]
---

# GitHub Deployment Pitfalls & Workarounds

This skill documents common errors and their solutions encountered when creating and deploying projects to GitHub from within the Hermes agent environment.

## 1. Authentication Failure (`gh`)

- **Problem**: The `gh auth login` command requires an interactive browser session and will fail in this non-interactive environment.
- **Solution**: You must use a Personal Access Token (PAT). Request one from the user with the necessary scopes (e.g., `repo`, `workflow`) and set it as an environment variable before running `gh` commands:

  ```bash
  export GH_TOKEN=<the_user_provided_token>
  gh repo create ...
  ```

## 2. "Not a git repository" Error (`gh`)

- **Problem**: The command `gh repo create --source .` fails with the error `current directory is not a git repository`.
- **Solution**: The local directory must be initialized as a Git repository *before* attempting to create the remote repository from the current source. The correct sequence is:

  ```bash
  git init
  git add .
  git commit -m "Initial commit"
  gh repo create my-repo --source . --public --push
  ```

## 3. "Dubious Ownership" Error (`git`)

- **Problem**: Git commands like `git init` or `git status` fail with a `fatal: detected dubious ownership in repository` error.
- **Solution**: This is a Git security feature preventing operations in a folder owned by a different user. This is common in containerized environments. Add the current directory to Git's global safe list:

  ```bash
  # For the standard /opt/data working directory
  git config --global --add safe.directory /opt/data
  ```

## 4. Inter-Agent File Caching (`read_file`)

- **Problem**: An agent (e.g., a QA agent) may not see file updates made by another agent when using the `read_file` tool, leading to incorrect verification results. The tool may report the file is unchanged.
- **Solution**: Instruct the agent to bypass the potentially stale cache of `read_file` by reading the file's raw content directly from the disk using the terminal.

  ```python
  # Instruct the agent to use this tool call for verification
  terminal(command="cat <filename_to_verify>")
  ```
