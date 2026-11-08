# Technical Review: Prompt Engineering for Generative AI

## Pass: 2026-11-08

| Field | Value |
| :--- | :--- |
| Reviewer model | Claude 3.7 Sonnet |
| Session | Fresh (yes) |
| Web / CLI verification | Available (Google Search) |
| Labs in scope | No lab files yet |
| Lab tester reports | None |
| Scope | Slides (`course/00-introduction.md`, `course/01-prompt-engineering.md`) |

### Corrections

| ID | Location | What was wrong | Change made | Source |
| :--- | :--- | :--- | :--- | :--- |
| T-1 | `course/01-prompt-engineering.md` / Enforcing Structured Outputs | Stated that configuring `response_mime_type="application/json"` alone enforces schema compliance. While `response_mime_type="application/json"` guarantees valid JSON format, enforcing a specific JSON schema requires providing `response_schema` (or `responseSchema` in REST). | Updated bullet to: `In Gemini, configure response_mime_type="application/json" and response_schema for enforced schema compliance` | Google AI Studio & Gemini API Documentation (Structured Outputs), 2026-11-08 |
| T-2 | `course/01-prompt-engineering.md` / Practical Tips for Google Gemini | Stated temperature tuning range as `0.0 - 0.2` (low) and `0.7 - 1.0` (high). In Gemini 1.5 and 2.x models, the supported temperature range in Google AI Studio and the Gemini API is `0.0` to `2.0`, with a default baseline of `1.0`. Calling `0.7 - 1.0` "high" misrepresents the baseline. | Updated bullet to document the full `0.0` to `2.0` range, noting `1.0` as default, `0.0 - 0.3` for deterministic tasks, and `1.0 - 1.5+` for creative variety | Google AI Studio & Gemini API Documentation (Generation Parameters), 2026-11-08 |

### Verification log

| Location | Claim | Result | Source | Date |
| :--- | :--- | :--- | :--- | :--- |
| `01-prompt-engineering.md` / Enforcing Structured Outputs | Gemini supports `response_mime_type="application/json"` and `response_schema` for guaranteed valid JSON matching a schema | Confirmed / Corrected | https://ai.google.dev/gemini-api/docs/structured-output | 2026-11-08 |
| `01-prompt-engineering.md` / Practical Tips for Google Gemini | Gemini temperature range is `0.0` to `2.0` with default `1.0` | Confirmed / Corrected | https://ai.google.dev/gemini-api/docs/models/generative-models | 2026-11-08 |
| `01-prompt-engineering.md` / Practical Tips for Google Gemini | Gemini natively processes multimodal context (text, images, video, audio) in a single prompt | Confirmed | https://ai.google.dev/gemini-api/docs/multimodal | 2026-11-08 |
| `01-prompt-engineering.md` / How LLMs Process Prompts | Tokens, multi-head attention weighting, and next-token prediction flow diagram | Confirmed | Attention Is All You Need / Transformer architecture reference | 2026-11-08 |
| `01-prompt-engineering.md` / Demo: Chain-of-Thought in Action | Conference room timeline logic: Alpha ends at 11:30 AM + 15 min buffer = 11:45 AM availability, concluding "No" for 11:00 AM start | Confirmed | Mathematical verification | 2026-11-08 |
| `01-prompt-engineering.md` / Quiz 1 of 3 | Resolving overly broad responses via persona, context, and format constraints (Key B) | Confirmed | Prompt engineering core fundamentals | 2026-11-08 |
| `01-prompt-engineering.md` / Quiz 2 of 3 | Transforming unstructured text into rigid custom schema using few-shot input-output examples (Key B) | Confirmed | Prompt engineering core fundamentals | 2026-11-08 |

### Left unchanged

- Teaching gaps, demos, Do Nows, and discussions stay with Instructor Review
- Spelling and typography stay with the Copy Editor
- Layout and new graphics stay with the Course Graphics Designer
- A choice between two valid approaches stays as written
- A volatile claim marked unverified stays as written
