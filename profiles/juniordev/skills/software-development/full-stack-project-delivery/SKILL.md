---
name: full-stack-project-delivery
description: Manages full-stack project delivery, from PRD to deployment.
---

### Full Stack Project Delivery Workflow

This skill outlines the end-to-end process for delivering a software project, coordinating the roles of Product Manager (PM), Technical Architect (TA), and QA Engineer.

#### **Phase 1: Requirements & Design**

1.  **Analyze PRD:** As the Technical Architect, receive the Product Requirements Document (PRD) from the Product Manager (<@1540511464450818122>). Critically analyze it. If requirements are unclear, absurd, or technically unsound, challenge them by @mentioning the PM.
2.  **Design Architecture:** Once requirements are clear, design the system architecture. Document this design in a file named `ARCHITECTURE.md`.
3.  **Break Down Tasks:** Decompose the project into small, testable, and trackable tasks. Document these in a `TASKS.md` file.

#### **Phase 2: Implementation & Verification**

1.  **Execute Tasks Sequentially:** Work through the tasks in `TASKS.md` one by one.
2.  **Announce Task Completion:** After completing a task, clearly state which task is complete.
3.  **Handoff to QA:** After each task completion, you MUST @mention the QA Engineer (<@1540500737937776660>) to verify the work against `TASKS.md` and `ARCHITECTURE.md`.
4.  **Await Verification:** Do not proceed to the next task until QA has confirmed verification of the current task.

#### **Phase 3: Deployment to GitHub Pages**

This phase is notoriously prone to errors related to environment, authentication, and Git state. Follow this sequence precisely.

1.  **Check Authentication:** The `gh` CLI requires authentication. The agent environment is typically not authenticated. Do not assume you are logged in. The most reliable method is to request a **GitHub Personal Access Token (PAT)** from the user with `repo` and `workflow` scopes.
    *   **User Guide:** If the user doesn't know how to create one, provide them with clear, step-by-step instructions. See `references/github-pat-guide.md`.

2.  **Set GH_TOKEN:** Once the user provides the token, export it as an environment variable for the current session: `export GH_TOKEN=<the_token_provided_by_user>`.

3.  **Initialize Git & Commit:**
    *   **Ownership Errors:** The agent environment might trigger a `fatal: detected dubious ownership` error. Fix this by running: `git config --global --add safe.directory /opt/data`
    *   **Initialize:** Run `git init`.
    *   **Configure Identity:** The agent's Git identity is likely unknown. Configure it before committing:
        ```bash
        git config --global user.name 'Hermes Coder'
        git config --global user.email 'hermes.coder@example.com'
        ```
    *   **Add Files Carefully:** `git add .` can fail if there are embedded/nested git repositories (e.g., from other tool caches). It is much safer to add project files explicitly: `git add index.html style.css script.js README.md`
    *   **Handle Lock Files:** If `git add` fails with an `index.lock` error, a previous process crashed. Remove the lock file: `rm .git/index.lock` and then re-attempt the `git add` command.
    *   **Commit:** Commit the staged files: `git commit -m "Initial commit"`.

4.  **Create Repository & Push:**
    *   Use the `gh repo create` command with the `--source=.` and `--push` flags. This creates the repository on GitHub and pushes your local commit in one step: `gh repo create your-repo-name --public --source=. --push`

5.  **Enable and Verify GitHub Pages:**
    *   **CRITICAL:** After deployment, always provide the user with the repository and the live GitHub Pages URL and ask them to verify it. Do not assume deployment succeeded just because the commands ran without error. The session showed a 404 error even after commands seemed to succeed.

### **Pitfalls & Workarounds**

*   **Agent Data Inconsistency/Caching:** If another agent (like QA) cannot see your file changes, their `read_file` tool may be reading from a stale cache. Instruct them to bypass it by reading the file's raw content using the terminal: `terminal(command="cat <filename>")`.
*   **Repetitive User Input:** If the user gets stuck in a loop sending the same command, do not just repeat the execution. Pause, analyze the state (e.g., why is `git commit` failing? Because nothing is staged), explain the problem, and execute the necessary *preceding* step first.
*   **Deployment Hand-off:** Do not declare the project "complete" until the deployment is live and verified by the user. The delivery is not finished until the user can access the artifact.
