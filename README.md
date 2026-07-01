# Agentic SOC Dashboard

A web dashboard for AI-assisted threat hunting. Analysts describe what they want to investigate in plain language, review AI-generated findings mapped to MITRE ATT&CK, track cases through to resolution, and see exactly what each investigation cost in AI spend.

This repo is the command center UI. It talks to a separate engine (the "SOC engine") that handles log querying and AI analysis.

---

**Dashboard**

View of the current case load: total cases, how many are still open, how many are high-confidence, and cumulative AI cost across all hunts run so far.
<img width="2546" height="1060" alt="image" src="https://github.com/user-attachments/assets/ed7736de-a7f0-4b70-ae31-b631f5a94dfc" />

---

**Threat hunt search**

The threat hunting interface. An analyst types what they want investigated in plain language, so no KQL is required. That prompt goes to the backend, where the AI agent picks the log table, fields, and time range on its own before running anything.
<img width="1274" height="434" alt="image" src="https://github.com/user-attachments/assets/031f3488-297e-44dd-8fd1-92731d39ad1f" />

---

**Threat hunt results**

After a hunt has completed, showing the finding cards with titles, confidence badges, and MITRE tags visible.
<img width="2545" height="640" alt="image" src="https://github.com/user-attachments/assets/5cbb40a2-6496-4d10-8a5f-3c8cee74a477" />

---

**Investigation queue**

Every open case, sorted so the highest-confidence findings surface first.
<img width="2434" height="778" alt="image" src="https://github.com/user-attachments/assets/13da8ef6-e6e7-49e3-9b3d-19fae42d8a69" />

---

**Case detail**

Clicking into a case opens the single-case view: a status dropdown to move it through open → investigating → resolved → ignored, the MITRE ATT&CK tactic and technique it was mapped to, and an analyst notes panel for logging what was reviewed and concluded.
<img width="2535" height="951" alt="image" src="https://github.com/user-attachments/assets/eb5f4cba-0f1b-492c-bf1f-17c9f1e4744d" />

<img width="2550" height="865" alt="image" src="https://github.com/user-attachments/assets/cccb9c54-82e3-4d70-9c69-35c5eeee6506" />

---

**Intel view**

Aggregates MITRE tactics and techniques across all cases, so recurring patterns are visible instead of only being readable one case at a time.
<img width="2549" height="590" alt="image" src="https://github.com/user-attachments/assets/28b5482f-83b2-4a63-aeb0-eaeca9ddfa68" />

---

## Platform stack

- Next.js (App Router)
- React + TypeScript
- Tailwind CSS
- REST integration with the Agentic SOC engine (FastAPI backend)

## Purpose

This dashboard gives an analyst a single place to:

- Run AI-assisted threat hunts against live log data
- Review findings mapped to MITRE ATT&CK tactics and techniques
- Triage and prioritize open investigations
- Track case status and analyst notes over time
- See the real dollar cost of every AI-driven investigation

## Core SOC pages

| Page | Function |
|---|---|
| `/hunts` | Threat hunting — describe an investigation in plain language, get AI-analyzed findings |
| `/results` | Full results table — every finding across every hunt, sortable by confidence and status |
| `/investigation` | Active investigation queue — open cases, highest confidence first |
| `/cases` | Case list and detail — evidence, status changes, analyst notes |
| `/memory` | Memory vault — every analyst note across every case, in one feed |
| `/intel` | Intel — MITRE ATT&CK tactics and techniques observed, aggregated across all cases |

## Capabilities

- **Natural-language threat hunting.** No KQL required. Describe what to investigate, and the backend's AI agent picks the table, fields, and time range on its own.
- **MITRE ATT&CK mapping.** Every finding includes tactic, technique, sub-technique, and a confidence rating.
- **Case lifecycle tracking.** Open → Investigating → Resolved → Ignored, with a persistent analyst notes thread per case.
- **Live AI cost accounting.** Real token usage from each hunt is priced against actual model rates and shown on the dashboard — not an estimate, the real cost.
- **Cross-case intelligence.** The Intel page aggregates tactics and techniques across every case so patterns are visible at a glance, instead of buried inside individual findings.

## Integration

This dashboard is the UI layer only — it does not query logs or call the AI directly. All of that happens in a separate backend service (the Agentic SOC engine) which:

- Accepts a natural-language hunt request and uses OpenAI function calling to select a Log Analytics query
- Validates the AI's chosen table and fields against an allow-list before executing anything
- Queries Azure Log Analytics for real log data
- Runs a second AI pass to analyze the logs and produce structured findings
- Persists cases, notes, and per-hunt cost data to a SQLite database

This dashboard fetches from that backend's REST API (`/api/hunt`, `/api/cases`, `/api/cases/{id}`, `/api/notes`, `/api/hunts/summary`, and related endpoints) and renders the results.

SOC Engine: https://github.com/Colby-hin/soc-engine
