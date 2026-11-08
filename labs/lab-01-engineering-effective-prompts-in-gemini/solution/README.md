# Solution: Engineering Effective Prompts in Gemini

## End State

A complete suite of tested prompts demonstrating foundational prompt engineering techniques in Google Gemini:

1. **Ambiguity Baseline**: A vague status prompt that exposes model hallucination and generic assumption generation.
2. **Structured Prompt**: An operational incident report prompt combining an explicit role, action verbs, delimited context, and markdown table constraints.
3. **Few-Shot Classification Prompt**: A demonstration-based prompt that triages unstructured customer support tickets into consistent JSON schemas.
4. **Chain-of-Thought Reasoning Prompt**: A step-by-step deduction prompt that accurately resolves a multi-step maintenance schedule conflict.
5. **Grounded Retrieval Prompt**: A reference-bounded prompt using `<policy>` tags and strict fallback constraints (`Information not found in policy.`) that prevents factual hallucinations.
6. **Bonus JSON Output Prompt**: A production-ready extraction prompt that converts unstructured incident logs into valid JSON with schema enforcement and conversational suppression.

---

## Verified Prompts and Expected Outputs

### Task 1: Ambiguous Baseline Prompt

#### Prompt
```text
Write a short status update about our project delay.
```

#### Expected Model Behavior
- The model invents a fictitious project name (such as "Project Titan" or "Website Redesign").
- The model fabricates arbitrary delay causes (such as unexpected vendor issues or staffing shortages).
- The model commits to an arbitrary revised deadline.
- Confirms that when critical context is omitted, large language models predict generic completions from web training data.

---

### Task 2: Structured Prompt with Role, Context, and Constraints

#### Prompt
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

#### Expected Model Output
```markdown
### Executive Status Update: Cluster Alpha Migration

- Customer Billing Database Migration (Cluster Alpha) is delayed by 48 hours due to secondary replica schema validation conflicts discovered during pre-cutover testing.
- Production systems are completely unaffected; all customer traffic continues running normally on the legacy database cluster.
- Cutover operations have been rescheduled for Friday, March 14 at 10:00 PM, with a mandatory progress checkpoint on Thursday, March 13 at 2:00 PM.

| Metric | Details |
| :--- | :--- |
| Project | Customer Billing Database Migration (Cluster Alpha) |
| Original Schedule | Wednesday, March 12 at 10:00 PM |
| Revised Schedule | Friday, March 14 at 10:00 PM |
| Root Cause | Secondary replica schema validation conflicts |
| Customer Impact | None (zero downtime, legacy cluster active) |
| Next Checkpoint | Thursday, March 13 at 2:00 PM |
```

---

### Task 3: Few-Shot Classification Prompt

#### Prompt
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

#### Expected Model Output
```json
{"ticket_id": "AUTO", "category": "Reporting", "priority": "High", "escalation": "Application Support"}
```

---

### Task 4: Chain-of-Thought Reasoning Prompt

#### Prompt
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

#### Expected Model Output
```markdown
1. **Automated backup completion**: Starts at 10:00 PM + 45 minutes = **10:45 PM**.
2. **Integrity verification completion**: Starts at 10:45 PM + 30 minutes = **11:15 PM**.
3. **Earliest permitted restart time**: Finishes verification at 11:15 PM + 15-minute quiesce buffer = **11:30 PM**.
4. **Comparison**: The requested restart time is 11:15 PM, but the earliest permitted time is 11:30 PM (a 15-minute deficit).
5. **Conclusion**: **NO**. The database cannot be restarted at 11:15 PM because integrity verification will have just finished and the mandatory 15-minute quiesce buffer will not expire until 11:30 PM.
```

---

### Task 5: Grounded Retrieval Prompt

#### Prompt
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

#### Expected Model Output (Query 1)
```text
An engineer can remain connected to the corporate VPN for up to 8 hours of continuous connection before the session automatically terminates and requires re-authentication using a physical hardware security key.
```

#### Follow-up Query for Unmentioned Topic
```text
Question: What is the maximum monthly reimbursement amount for an employee's home internet service?
```

#### Expected Model Output (Query 2)
```text
Information not found in policy.
```

---

### Bonus Task 6: Strict JSON Output Prompt

#### Prompt
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

#### Expected Model Output
```json
{
  "incident_id": "INC-2026-991",
  "affected_service": "Payment gateway",
  "severity": "HIGH",
  "root_cause_summary": "Redis connection pool exhaustion on checkout-worker cluster caused severe request queue backlog.",
  "action_items": [
    "Update Terraform Redis module defaults by Tuesday",
    "Configure PagerDuty alert for Redis pool utilization exceeding 80%"
  ]
}
```

---

## How to Verify

1. Open Google Gemini in your web browser (`https://gemini.google.com/`).
2. Copy and paste each prompt into the Gemini chat interface.
3. Compare the generated responses against the expected model behaviors documented above:
   - **Task 1**: Confirm the model invents details when given an ambiguous prompt.
   - **Task 2**: Confirm the structured prompt produces exactly three bullets, a markdown table, and zero conversational filler.
   - **Task 3**: Confirm the few-shot prompt outputs a single JSON record matching the example schema.
   - **Task 4**: Confirm the model works through intermediate deduction steps and arrives at the correct negative decision (`NO`) with the 11:30 PM earliest time.
   - **Task 5**: Confirm the grounded prompt answers the first question factually from the text, and returns the exact fallback string `Information not found in policy.` for the ungrounded question.
   - **Task 6**: Confirm the model returns raw JSON conforming to the schema without conversational padding.

---

## Built With

- Google Gemini Web Interface (`gemini.google.com`)
- Models: Gemini 1.5 Flash / Gemini 1.5 Pro / Gemini 2.0 Flash
- Verified: March 2025
- Unverified items: None. Prompts use standard instruction-following patterns compatible across all Gemini model tiers.
