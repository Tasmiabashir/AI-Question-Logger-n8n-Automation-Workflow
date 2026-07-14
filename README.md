# AI Question Logger — n8n Automation Workflow

An end-to-end no-code/low-code automation built with **n8n** that takes a user's question through a chat interface, sends it to **OpenAI (GPT-4o-mini)**, and automatically logs both the question and the AI-generated answer into **Google Sheets** — creating a permanent, searchable record of every AI interaction.

This pattern mirrors how real companies log AI chatbot conversations for quality review, analytics, and compliance.

## Demo

**Flow:** Chat Input → OpenAI → Google Sheets

```
[When chat message received] ──► [Message a Model (OpenAI)] ──► [Append Row in Sheet (Google Sheets)]
```

Example run:
| Question | Answer |
|---|---|
| What is machine learning in one sentence? | Machine learning is a subset of artificial intelligence that enables systems to learn from data and improve their performance over time without being explicitly programmed. |

## Tech Stack

- **n8n Cloud** — workflow automation platform (visual node-based builder)
- **OpenAI API (GPT-4o-mini)** — LLM for answering user questions
- **Google Sheets API (OAuth2)** — persistent logging of Q&A pairs
- **n8n Expressions** — dynamic data mapping between nodes (`{{ $json.chatInput }}`)

## Workflow Structure

```
ai-question-logger/
├── workflow.json          # Exported n8n workflow (import-ready)
├── screenshots/
│   ├── workflow-canvas.png    # The 3-node pipeline
│   └── sheet-output.png       # Logged results in Google Sheets
└── README.md
```

### Node Breakdown

1. **When chat message received (Chat Trigger)**
   Entry point of the workflow. Provides a chat UI and outputs the user's message as `chatInput`.

2. **Message a Model (OpenAI)**
   - Resource: `Text` | Operation: `Message a Model`
   - Model: `gpt-4o-mini`
   - Prompt (Expression): `{{ $json.chatInput }}`

3. **Append Row in Sheet (Google Sheets)**
   - Operation: `Append Row`
   - Column mapping (Expressions):
     - `Question` → `{{ $('When chat message received').item.json.chatInput }}`
     - `Answer` → `{{ $json.output[0].content[0].text }}`

## Setup

1. Create a free account at [n8n.io](https://n8n.io) (cloud trial includes free OpenAI credits)
2. Create a new workflow → menu (⋯) → **Import from File** → select `workflow.json`
3. Create a Google Sheet with headers `Question` (A1) and `Answer` (B1)
4. Open the **Append Row in Sheet** node → connect your Google account via **Sign in with Google** (OAuth2) → select your spreadsheet and sheet
5. Open the **Message a Model** node → select the built-in n8n OpenAI credential (or add your own API key)
6. Click **Open chat**, ask a question, and watch the row appear in your sheet ✅

## Key Learnings

- Mapping data between nodes using n8n Expressions, including reaching back to earlier nodes with `$('Node Name').item.json`
- Reading nested JSON output structures (`output[0].content[0].text`) to extract the LLM response
- Connecting third-party services (Google OAuth2) securely without hardcoding credentials
- Debugging node-level validation errors using n8n's execution logs

## Notes

- The n8n cloud trial includes free OpenAI credits (no API key required) — for production use, swap in your own OpenAI credential.
- The Google Sheets node uses OAuth2, so no API keys are stored in the workflow file.
- This workflow can be extended easily: add a Slack notification node, sentiment analysis on answers, or swap Google Sheets for a database like Postgres.

## Author

**Tasmia Bashir**
- GitHub: [Tasmiabashir](https://github.com/Tasmiabashir)
- LinkedIn: [tasmia-bashir](https://www.linkedin.com/in/tasmia-bashir/)
