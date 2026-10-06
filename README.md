# Meridian Private Clients — AI Proposal Agent

This repository contains my solution for the Meridian Wealth AI Proposal Agent challenge. The agent successfully translates unstructured Portfolio Manager notes and voice recordings into the validated JSON schema required by the proposal tool.

## Architecture & Tech Stack

The solution uses a decoupled architecture to separate the user interface from the heavy lifting of the AI orchestration.

| Component | Technology | What it does |
| :--- | :--- | :--- |
| **Frontend Interface** | Vanilla JS & Tailwind | A custom, floating panel embedded in `challenge-generator.html` to capture text and audio without disrupting the preview. |
| **Orchestration Engine** | n8n (Self-hosted) | Acts as the central backend, handling webhook reception, conditional routing, and API communication. |
| **Speech-to-Text** | OpenAI Whisper | Automatically transcribes uploaded audio files (`.m4a`, `.mp3`) into text via the n8n pipeline. |
| **Core AI Agent** | LLM | Extracts unstructured data and maps it strictly to `proposal-schema.md`, omitting missing keys to preserve system defaults. |

## How it works

1. **Data Capture:** The user interacts with the floating panel to paste text or attach an audio file. Audio is converted to a Base64 string for secure transmission.
2. **Dynamic Routing:** An n8n webhook receives the payload. An IF node checks for audio; if found, it routes through Whisper for transcription before merging with any typed notes.
3. **Schema Enforcement:** The LLM applies strict zero-shot extraction to format the output. It intelligently omits missing data (to avoid fabricating figures) and appends a `[TO CONFIRM]` tag for ambiguity.
4. **Rendering:** The JSON payload is returned to the frontend and injected directly into `window.loadProposal(...)`.

## How to run it

This solution requires no local backend setup, as the n8n orchestration layer is hosted live. 

1. Open `challenge-generator.html` in any modern browser.
2. Locate the floating **Proposal Agent** panel in the bottom right corner.
3. **Run a text test:** Paste the contents of `sample-inputs/01-notes-retiree-income.txt` into the text box and click **Generate Proposal**.
4. **Run an audio test:** Click the paperclip/attachment icon, upload a sample audio file (to simulate `04-voice-note-transcript.txt`), and click **Generate Proposal**. 

## What I'd do next

With an additional 4–8 hours, I would focus on extending the agent's robust omnichannel capabilities and error handling:

| Feature | Implementation Plan |
| :--- | :--- |
| **WhatsApp Integration** | Connect the Meta WhatsApp Business API to the existing n8n audio branch. PMs could forward voice notes while driving and instantly receive the finalized PDF in their chat. |
| **Strict JSON Validation** | Add a JSON Validator node directly inside the n8n workflow. If the LLM hallucinates a key, it would catch the error and trigger an automatic retry loop rather than breaking the frontend. |
| **Interactive Ambiguity** | Instead of simply flagging missing data on the final PDF, the agent would pause the workflow and prompt the PM via the UI to clarify missing constraints (like horizon or amount) before generating. |
| **API Security** | Move the exposed n8n webhook URL behind a secure API gateway to authenticate requests and manage rate limiting for production. |
