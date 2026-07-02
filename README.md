# Agentic SOC Dashboard 

## What this is

This is a web dashboard built on top of an existing AI threat hunting engine. The engine already worked on its own as a command line tool. It could query security logs and use AI to analyze them, but it only ran in a terminal. This dashboard sits on top of that engine. It makes it easier to search, review, and document findings, and it brings useful tools like file reputation checks directly into the interface instead of requiring separate manual steps.

<img width="1710" height="1089" alt="image" src="https://github.com/user-attachments/assets/9bf70ceb-d12e-49f5-94eb-3cd4e33f61d5" />


## Capabilities

- Natural language threat hunting, no special query syntax required
- Findings automatically mapped to MITRE ATT&CK tactics and techniques, with a confidence rating
- Case lifecycle tracking, moving a finding through open, investigating, resolved, or ignored
- A permanent analyst notes thread on every case
- File hash reputation checks against VirusTotal
- Real AI cost tracked per hunt, based on actual token usage rather than an estimate
- Cross case pattern views, showing which attack techniques show up most often

## Platform stack

- Python
- FastAPI
- SQLite
- OpenAI API
- Azure Log Analytics
- Microsoft Defender for Endpoint
- Next.js (React + TypeScript)
- Tailwind CSS
- VirusTotal API

## Setup and what each piece does

### Python and FastAPI

The engine started as a plain Python script that only worked if you ran it directly in a terminal. FastAPI is a Python package that turns regular functions into web addresses a browser can call. Installing it is what allowed a website to trigger a hunt instead of only being able to run it from a terminal.

Uvicorn is the program that actually runs the FastAPI app and keeps it listening for requests. FastAPI defines what should happen. Uvicorn is what's running in the background and answering when a request comes in.

### SQLite

SQLite comes built into Python, so it didn't need a separate install. It's the database that stores every case, every analyst note, and the cost of every hunt. It's what gives the tool a memory. Before this, results just printed to the screen once and were gone.

### The requests library

This is a small, common Python package used for making calls to other services online. It's what lets the backend reach out to VirusTotal and ask whether a specific file hash has been seen before.

### Node.js

Node.js lets JavaScript run outside of a browser. It's needed because the dashboard itself, built with Next.js and React, is written in JavaScript and TypeScript. Without Node.js installed, none of the frontend code can run.

### Next.js, React, and Tailwind CSS

React is the library that builds the actual interface, things like buttons, forms, and pages. Next.js is a framework built on top of React that organizes the app into pages automatically and handles a lot of setup that would otherwise need to be done by hand. Tailwind CSS is the styling system used to keep everything looking consistent without writing custom CSS for every element.

## How it fits together

The Python and FastAPI side and the Next.js side are two separate programs running at the same time. They talk to each other over the network. The dashboard sends a request, something like "run a hunt" or "give me all cases," and the engine sends back an answer. None of the engine's original logic had to change for this to work. The dashboard was built entirely as a layer on top, calling into the engine rather than replacing any part of it.

## An example of how a request moves through the system 

## Threat hunt search

<img width="1703" height="349" alt="image" src="https://github.com/user-attachments/assets/bae19aa1-1fc4-42ad-b6d3-80c5e7ccf0ee" />

---
## Threat hunt findings

<img width="1697" height="1197" alt="image" src="https://github.com/user-attachments/assets/33d3c753-15ad-4029-a76b-215d7d6ca18a" />

---
## Results section

Every finding across every hunt, in one table.
<img width="1695" height="508" alt="image" src="https://github.com/user-attachments/assets/bd4f6a18-ea2e-4562-8187-c0813feecd51" />

---
## Case detail

This case is set to investigating. Status options are open, investigating, resolved, and closed.
<img width="1707" height="765" alt="image" src="https://github.com/user-attachments/assets/c32c43aa-7c8f-409a-be5c-ae0db3b1dc9f" />



---
## Intel section 

Tactics and techniques aggregated across all cases, clickable to see the cases behind each one.
<img width="1467" height="785" alt="image" src="https://github.com/user-attachments/assets/4cb593ba-bd00-4316-82e5-1cfe7b3a5d86" />




---

## Behind the scenes flow

```mermaid
flowchart TD
    A[Analyst types a request in the dashboard] --> B[Dashboard sends it to the engine's API]
    B --> C[AI reads the request and picks a table, fields, and time range]
    C --> D{Is the AI's choice on the allowed list?}
    D -->|No| E[Request is blocked]
    D -->|Yes| F[Real log data is pulled from Azure]
    F --> G[A second AI pass reads the logs and writes findings]
    G --> H[Findings are saved as cases in the database]
    H --> I[Dashboard displays the results]
```



SOC Engine: https://github.com/Colby-hin/soc-engine
