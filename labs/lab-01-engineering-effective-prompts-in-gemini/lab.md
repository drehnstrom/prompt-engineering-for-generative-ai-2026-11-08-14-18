# Engineering Effective Prompts in Gemini

## Time Required

30 minutes

## Overview

In this lab, you will build, test, and iteratively refine structured prompts within Google Gemini. You will examine the operational risks of ambiguous instructions, apply foundational components including personas, context delimiters, and format constraints, guide multi-step reasoning using chain-of-thought prompting, and ground responses in source documentation to eliminate hallucinations.

### You learn how to:
- Identify weaknesses in ambiguous prompts and apply structured components including role, task, context, and constraints.
- Implement few-shot prompting to classify text into a consistent structured format.
- Apply chain-of-thought reasoning to solve multi-step operational and scheduling problems.
- Ground model responses in source reference documents to eliminate hallucinations.
- Iteratively refine prompts to enforce concise, structured outputs.

## Scenario

You are an operations analyst at a growing technology company. Your team receives raw incident dispatches, customer support tickets, and maintenance scheduling requests daily. Without structured prompts, generative AI assistants produce generic, rambling, or hallucinated responses that require time-consuming manual rework. In this lab, you construct and refine prompt templates in Google Gemini to deliver accurate, audit-compliant, and production-ready outputs.

## Lab Instructions

### Task 1: Sign in to Gemini and evaluate an ambiguous prompt

In this task, you access the Google Gemini web interface and evaluate how an underspecified prompt forces the model to invent arbitrary facts.

1. Open a new web browser tab and navigate to [https://gemini.google.com/](https://gemini.google.com/).

2. Sign in using your Google account credentials if prompted.

3. In the message box at the bottom of the Gemini window, enter the following ambiguous prompt:

```text
Write a short status update about our project delay.
```

4. Click the Submit button (or press Enter) to send your prompt.

5. Review the generated response and identify the details that the model fabricated:
- What project name or business domain did the model invent?
- What technical or organizational reason did it provide for the delay?
- What revised completion date did it commit to?

> [!NOTE]
> Large language models are probabilistic next-token predictors. When you omit critical operational context, the model predicts completions based on general web training data rather than your actual business reality.

### Task 2: Build a structured prompt with role, context, and constraints

In this task, you assemble a prompt using core structural building blocks: persona, explicit action verbs, delimited context, and output constraints.

1. In the Gemini interface, start a fresh conversation by clicking **New chat** in the left navigation sidebar.

2. In the prompt input area, enter the following structured prompt:

```text
Role: Senior Cloud Infrastructure Specialist
Task: Write an operational status update for executive leadership regarding a planned database migration delay.

Context:
- Project: Customer Billing Database Migration (Cluster Alpha)
- Original Schedule: Wednesday, March 12 at 10:00 PM
- Current Status: Delayed by 48 hours due to secondary replica schema validation conflicts
- Customer Impact: None; primary production traffic remains on the legacy cluster
- Revised Rollout Window: Friday, March 14 at 10:00 PM
- Next Checkpoint: Thursday, March 13 at 2:00 PM engineering sync

Constraints:
- Exactly three concise bullet points under a summary heading
- A markdown table with columns: Metric | Details
- Tone: Objective, technical, and executive-ready
- Do not include conversational greetings, sign-offs, or conversational filler
```

3. Submit the prompt to Gemini.

4. Inspect the output and verify that it adheres to every constraint:
- The response opens immediately with the requested summary without conversational pleasantries.
- Exactly three bullet points appear under the summary heading.
- The metrics and schedule details appear in a properly formatted markdown table.

> [!TIP]
> Focus personas on specific professional functions, responsibilities, and evaluation criteria rather than theatrical or exaggerated titles.

### Task 3: Enforce rigid classification with few-shot prompting

In this task, you provide demonstration input-output pairs to guide Gemini into classifying unstructured support tickets into a consistent JSON schema.

1. Click **New chat** in the left navigation sidebar to clear previous conversational context.

2. In the prompt input box, enter the following few-shot prompt:

```text
Classify customer support tickets into JSON records. Follow the exact schema demonstrated below.

Input: "My credit card was charged twice for order 88412, and both charges are posted."
Output: {"ticket_id": "AUTO", "category": "Billing", "priority": "High", "escalation": "Finance Ops"}

Input: "Can you add an option to export our monthly reports to CSV format?"
Output: {"ticket_id": "AUTO", "category": "Feature Request", "priority": "Low", "escalation": "Product Team"}

Input: "Our single sign-on integration is failing for all European employees this morning."
Output: {"ticket_id": "AUTO", "category": "Authentication", "priority": "Critical", "escalation": "Security Engineering"}

Input: "The weekly automated sales performance email failed to arrive in my inbox today, and our director needs the figures for a 2:00 PM meeting."
Output:
```

3. Submit the prompt to Gemini.

4. Verify that Gemini completes the pattern by returning a single JSON record that matches the schema, categorizing the ticket as reporting issues with elevated priority.

> [!IMPORTANT]
> Providing two or three concrete demonstration examples teaches schema rules, casing conventions, and edge-case behavior much more effectively than writing multiple paragraphs of abstract formatting instructions.

### Task 4: Guide multi-step reasoning using chain-of-thought

In this task, you compare a direct query against a step-by-step reasoning prompt to solve a maintenance scheduling problem accurately.

1. Click **New chat** to start a clean session.

2. First, submit a direct query that asks for an immediate conclusion without intermediate steps:

```text
Automated snapshot backup starts at 10:00 PM and takes 45 minutes. Integrity verification scripts run immediately after backup and take 30 minutes. A 15-minute system quiesce buffer is required before database restart. Can our engineering team restart the database at 11:15 PM? Answer YES or NO.
```

3. Note whether the model incorrectly answers “YES” by confusing the verification script completion time (11:15 PM) with the earliest permitted restart time after the buffer.

4. Submit the following chain-of-thought prompt that breaks the calculation into sequential deduction steps:

```text
Task: Determine whether the platform engineering team can perform a database restart at 11:15 PM without violating maintenance constraints.

Context:
- Automated snapshot backup begins at 10:00 PM and takes exactly 45 minutes.
- Integrity verification scripts must execute immediately following the backup and require exactly 30 minutes.
- A mandatory 15-minute system quiesce buffer is required after verification completes before database services can be restarted.
- Platform engineering requested a restart window at 11:15 PM.

Instruction:
Think step by step before stating your final conclusion.
1. Determine the exact time the automated backup completes.
2. Determine the exact time the integrity verification scripts finish.
3. Calculate the earliest permitted database restart time by adding the mandatory 15-minute quiesce buffer.
4. Compare the earliest permitted restart time with the requested 11:15 PM window.
5. Conclude with a clear YES or NO decision and a one-sentence rationale.
```

5. Confirm that Gemini calculates each intermediate timestamp (10:45 PM backup completion, 11:15 PM verification completion, 11:30 PM earliest restart time) and correctly concludes with **NO**.

> [!NOTE]
> Requesting step-by-step deductions consumes slightly more output tokens, but sharply reduces mathematical, logical, and scheduling errors.

### Task 5: Ground responses in reference documentation to eliminate hallucinations

In this task, you provide reference policy text inside XML-style delimiters and enforce a strict fallback rule to prevent the model from inventing non-existent rules.

1. Click **New chat** to start a clean conversation.

2. Enter the following grounded prompt containing explicit policy source text and fallback constraints:

```text
You are an internal corporate policy assistant. Answer user questions using ONLY the text inside the <policy> block below.

Rules:
- Base your answers strictly on the facts directly stated in the policy text.
- Do not assume, extrapolate, or infer details not directly stated.
- If the policy text does not contain the answer, reply with this exact phrase and nothing else: "Information not found in policy."
- Do not include conversational remarks, pleasantries, or speculative explanations.

<policy>
Remote Access and Telework Security Policy (Revision 4.2)
1. Virtual Private Network (VPN) sessions automatically terminate after 8 hours of continuous connection. Users must re-authenticate using their physical hardware security key.
2. Production database access through VPN is strictly restricted to designated on-call engineers holding active Level 2 Privileged Access approval.
3. Personal computing devices (BYOD) may access corporate email and calendar systems through the mobile browser portal only. Storing corporate documents on local BYOD storage is strictly prohibited.
4. Lost or compromised security tokens must be reported to the Security Operations Center (SOC) hotline at extension 4499 within two hours of discovery.
</policy>

Question: How long can an engineer remain connected to the corporate VPN before being required to re-authenticate?
```

3. Submit the prompt and confirm that Gemini answers factually using the 8-hour limit and physical hardware security key requirement stated in Rule 1.

4. In the same chat, submit a question about a topic that is not addressed anywhere in the policy text:

```text
Question: What is the maximum monthly reimbursement amount for an employee's home internet service?
```

5. Verify that Gemini returns the exact required fallback phrase without fabricating an allowance: `Information not found in policy.`

> [!WARNING]
> If you do not give a generative model permission to say “I do not know” or provide an explicit fallback phrase, it will often fabricate plausible corporate rules to satisfy the user request.

### Bonus Task 6: Iteratively refine a prompt for strict JSON output

In this task, you parse raw incident text into a structured JSON record suitable for automated ingestion, and iteratively eliminate unwanted markdown wrappers.

1. Click **New chat** to start a clean session.

2. Enter the following extraction prompt:

```text
Role: Site Reliability Engineering Incident Parser
Task: Parse the raw incident dispatch text below into a clean JSON object matching the schema specification.

Schema:
{
  "incident_id": "string",
  "affected_service": "string",
  "severity": "CRITICAL" | "HIGH" | "MEDIUM" | "LOW",
  "root_cause_summary": "string (maximum 20 words)",
  "action_items": ["string"]
}

Constraints:
- Output valid JSON only
- Do not wrap the output in markdown code blocks or backticks
- Do not include conversational introductory text or concluding commentary
- Action items must be imperative statements

Raw Incident Dispatch:
"INC-2026-991: Payment gateway latency spiked to 4,800ms between 03:15 UTC and 03:42 UTC. Redis connection pool exhaustion on the checkout-worker cluster caused request queue backlog. We scaled the Redis cluster connection ceiling from 500 to 2,000 connections and cleared the worker backlog. Follow-up: update the Terraform Redis module defaults by Tuesday, and configure a PagerDuty alert for Redis pool utilization exceeding 80%."
```

3. Submit the prompt to Gemini.

4. Inspect the response. Check whether Gemini returned valid JSON with the exact expected keys and values.

5. If Gemini wrapped the JSON in markdown code blocks (such as triple backticks) or included conversational introductory text, refine your prompt by submitting the following correction:

```text
Reformat your previous response to output raw JSON text only. Remove all markdown backticks, code blocks, and conversational commentary.
```

6. Confirm that the refined output consists strictly of the raw JSON payload ready for direct software parsing.

## Congratulations!

In this lab, you have:
- Identified weaknesses in ambiguous prompts and applied structured components including role, task, context, and constraints.
- Implemented few-shot prompting to classify text into a consistent structured format.
- Applied chain-of-thought reasoning to solve multi-step operational and scheduling problems.
- Grounded model responses in source reference documents to eliminate hallucinations.
- Iteratively refined prompts to enforce concise, structured outputs.
