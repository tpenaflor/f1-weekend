---
name: gcp-billing-vertex-ai-reporting
description: Manages GCP Vertex AI token usage and billing reporting.
author: hermes-banker
version: 0.1
category: mlops
---

# GCP Vertex AI Billing and Token Usage Reporting Workflow

## Trigger Conditions
Use this skill when a user requests a report on Google Cloud Platform (GCP) Vertex AI model token usage and associated billing, or when daily monitoring of these metrics is required.

## Overview
This workflow describes the process of obtaining GCP billing data and Vertex AI metrics, typically involving delegation for script development, handling API rate limits, debugging environment issues, and finally generating detailed reports.

## Steps

1.  **Identify Data Requirements:** Clarify specific data points needed (e.g., tokens per model, daily costs, currency, project/region).
2.  **Delegate Script Design:** If a script to fetch GCP billing data or Vertex AI metrics is not available, delegate the task to the Product Manager (`@hermes-pm`) to design the script. Provide detailed requirements for the script's functionality, data points, and output format.
3.  **Await Script Development:** Once the Product Manager provides the Product Requirements Document (PRD), the Coder (`@hermes-coder`) will implement the script.
4.  **Handle API Rate Limiting (if applicable):** If Vertex AI API rate limiting (HTTP 429 errors) occurs during script execution or validation:
    *   Instruct the Coder to investigate the issue and implement robust retry logic with exponential backoff and jitter. The `tenacity` library is a recommended solution for Python.
    *   Ensure the Coder directly implements these technical details, as it falls under their responsibility.
5.  **Debug Environment/Dependency Issues:** If `ModuleNotFoundError` or similar dependency issues arise during script execution:
    *   Identify the correct Python interpreter and virtual environment (e.g., using `uv python find` and then executing with the specific venv path like `/opt/data/.venv/bin/python3`).
    *   Ensure all necessary Google Cloud client libraries (`google-cloud-logging`, `google-cloud-bigquery`) are installed within the correct environment.
6.  **Execute the Script:** Once the script is finalized and environmental issues are resolved, execute the script with appropriate `project_id`, `bigquery_billing_table`, and date range arguments.
    *   Example: `/opt/data/.venv/bin/python3 /opt/data/gcp_vertex_ai_billing_metrics.py --project_id "YOUR_GCP_PROJECT_ID" --bigquery_billing_table "YOUR_BILLING_PROJECT.YOUR_BILLING_DATASET.gcp_billing_export_v1_YOUR_BILLING_ID" --output_format json --start_date "$(date -d "yesterday" +"%Y-%m-%d")" --end_date "$(date +"%Y-%m-%d")"`
7.  **Process Output Data:** Collect the script's output (preferably JSON).
8.  **Generate Reports:** Create daily reports in both Markdown (MD) and PDF formats, summarizing token usage, costs, and any detected anomalies.

## Pitfalls

*   **Rate Limiting:** Vertex AI APIs can be aggressive with 429 errors. Robust retry mechanisms are essential for script reliability.
*   **Authentication:** Ensure Google Cloud Application Default Credentials (ADC) are properly configured for the execution environment.
*   **BigQuery Billing Export:** The BigQuery billing table must be correctly configured and accessible to the service account running the script.
*   **Module Not Found Errors:** Verify that all required Python packages are installed in the *correct* virtual environment associated with the Python interpreter used for execution. Do not assume system-wide installations.
*   **Role Confusion:** Clearly communicate technical implementation tasks to the Coder and product requirements to the Product Manager, respecting their defined roles.

## Verification
*   Successful script execution without API errors or environment issues.
*   Output data is in the expected format (JSON/CSV) and contains all required metrics.
*   Generated reports accurately reflect the fetched data.