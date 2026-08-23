---
name: hermes-discord-setup
description: Hermes Discord home channel setup guidance.
author: Gemini
---

# Hermes Discord Home Channel Setup and Troubleshooting

## Trigger Conditions
Use this skill when encountering persistent messages or agent blocking related to the "Discord home channel not set." This often manifests as agents being unable to deliver messages, reports, or proceed with tasks.

## Problem Description
Hermes agents require a designated Discord "home channel" for reliable delivery of cron job results, reports, and cross-platform messages. When this is not set, all agents become blocked, leading to repeated interruptions and an inability to complete tasks that rely on communication or output delivery.

## Core Issue
Setting the Discord home channel is a **manual, user-initiated action** within the Discord client. Agents **cannot** programmatically execute the `/sethome` command or determine its status directly through Discord APIs.

## Impact of Unset Home Channel
*   **Agent Blockage**: All agents (PM, Coder, QA, Banker) will become completely blocked from executing tasks.
*   **Communication Failure**: Agents cannot reliably deliver any output (e.g., reports, task summaries, error messages).
*   **Constant Interruptions**: The system will generate persistent "No home channel is set" notifications, often interrupting ongoing tasks.

## Recommended Workflow
1.  **Identify the issue**: The primary signal is repeated messages about the "Discord home channel not set."
2.  **Inform the user**: Clearly communicate that setting the home channel is a manual action required from them.
3.  **Instruct the user**: Ask the user to type `/sethome` directly in the *desired Discord channel*.
4.  **Confirm user action**: If the user is unresponsive, consider using the `clarify` tool to inquire if they have attempted to use `/sethome` or encountered any errors.
    *   **Pitfall**: Do not continuously re-iterate the same message without seeking clarification if the user remains unresponsive. Attempt to understand *why* they haven't acted.

## Agent Capabilities / Limitations
*   Agents **can** `tool_search` and `tool_describe` `discord_admin` tools to inspect Discord server/channel metadata (`list_guilds`, `list_channels`, `channel_info`).
*   Agents **cannot** find an API call within `discord_admin` that directly sets the Hermes home channel. The `discord_admin` tool is for general server management, not Hermes-specific commands.
*   Agents **cannot** execute the `/sethome` command on behalf of the user.

## Verification
The issue is resolved when the persistent "No home channel is set" messages stop and agents can successfully deliver messages or reports.

## References
*   This session's conversation (for context on repeated blocking and agent pleas).
