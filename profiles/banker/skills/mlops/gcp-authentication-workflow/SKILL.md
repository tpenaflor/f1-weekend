---
name: gcp-authentication-workflow
category: mlops
description: Manages secure requests for GCP service account credentials.
---

# GCP Authentication Workflow

This skill outlines the procedure for requesting and securely handling GCP service account credentials from the user when required for GCP API interactions.

## Trigger Conditions

*   When a script or tool requires GCP authentication (e.g., Google Cloud SDK, client libraries).
*   When a `google.auth.default()` or similar authentication method reports missing credentials.

## Steps

1.  **Identify the need for credentials:** The script or tool execution fails with an authentication error (e.g., "Your default credentials were not found").
2.  **Determine required access:** Review the requirements (e.g., PRD) to identify the minimum necessary permissions for the service account (e.g., read-only access to billing and Vertex AI usage data).
3.  **Request credentials from the human user:**
    *   Clearly state that the request for credentials is directed to the *human user* or an *authorized entity*, not to other automated agents (e.g., PM).
    *   Specify the exact format required (e.g., JSON content of the service account key).
    *   Emphasize the sensitive nature of the credentials and that they will be handled securely (e.g., saved to a temporary file, configured via `GOOGLE_APPLICATION_CREDENTIALS`).
    *   Explicitly mention the necessary read-only access for security.
4.  **Await user input:** Remain in a waiting state until the user provides the JSON content.
5.  **Securely store credentials (Coder's task):** Once provided, the Coder (or equivalent agent) will be responsible for:
    *   Saving the JSON content to a temporary, securely managed file.
    *   Setting the `GOOGLE_APPLICATION_CREDENTIALS` environment variable to point to this file.
    *   Ensuring the file is deleted or secured after use as appropriate.

## Pitfalls

*   **Requesting credentials from the wrong entity:** Do not ask other agents (e.g., PM) for sensitive credentials; always direct such requests to the human user operating the environment.
*   **Unclear request:** Ensure the request specifies the exact format, required access, and purpose of the credentials.
*   **Insecure handling:** Emphasize that credentials will be handled securely and temporarily.

## Verification

*   The script or tool successfully authenticates to GCP and proceeds with its intended operations.