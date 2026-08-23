---
name: multi-agent-workflow-best-practices
category: autonomous-ai-agents
description: "Multi-agent collaboration and communication best practices."
---

# Multi-Agent Workflow Best Practices

Effective collaboration in multi-agent systems relies on clear understanding of roles, efficient communication, and structured handoffs. This skill outlines best practices to ensure smooth operation and prevent common pitfalls.

## 1. Clear Role Delineation

Each agent (e.g., Product Manager, Coder, QA Engineer, Banker) has a defined scope of responsibility. Avoid taking on tasks outside your designated role unless explicitly instructed or in an emergency.

**Pitfall: Role Overlap / Interference**
*   **Problem:** An agent attempts to perform tasks or make decisions that fall under another agent's primary responsibility (e.g., a QA Engineer attempting to define product requirements, or a Product Manager debugging code).
*   **Solution:** Respect defined roles. When a task requires action from another role, direct the request clearly to the appropriate agent.
    *   **Example for QA/Coder:** As a QA Engineer, if a script fails due to missing dependencies or an implementation bug, report it to the Coder for resolution, rather than attempting to fix it yourself (unless explicitly instructed to do so).
    *   **Example for PM:** When interacting with a "Product Manager" role, focus on delivering final artifacts that meet requirements. Avoid engaging them in technical implementation details, debugging steps, or environmental setup issues. They expect to review the *outcome*, not the *process* of technical execution.

## 2. Effective Communication and Handoffs

Ensure that information passed between agents is clear, concise, and actionable.

*   **When reporting issues:** Provide all necessary context, error messages, and reproduction steps.
*   **When handing off tasks:** Clearly state the goal, expected outcome, and any dependencies.
*   **Awaiting next steps:** Explicitly state when you are waiting for another agent's action to proceed.

## 3. Verifiable Artifacts

Whenever possible, produce verifiable artifacts (e.g., code, test reports, generated documents) that can be independently reviewed by other agents, especially by the Product Manager. This reduces ambiguity and ensures alignment with requirements.
