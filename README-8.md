# BiomarkerRAG

An agentic RAG workflow for querying biomarker and companion diagnostics research via Slack. Built with n8n, Vectorize.io, and Pinecone.

## What It Does

A researcher drops a PDF into a Google Drive folder. The pipeline automatically ingests, chunks, embeds and stores it in a vector database. Anyone on the team can then open Slack, mention @BiomarkerBot in the #biomarker-research channel, ask a question in plain English, and get a response pulled directly from the research literature.

No digging through PDFs. No waiting for a scientist to be available.

## Architecture

```
Google Drive → Vectorize.io → Pinecone → n8n AI Agent → Slack
```

1. PDFs land in a Google Drive folder
2. Vectorize.io detects new documents, extracts text, chunks by paragraph at 500 tokens with 50 token overlap
3. OpenAI text-embedding-3-small generates 1536 dimension embeddings
4. Vectors are stored in a Pinecone dense index
5. A Slack message mentioning @BiomarkerBot triggers the n8n workflow
6. The AI Agent queries Pinecone for the most relevant chunks
7. OpenAI generates a plain English response from the retrieved context
8. The response is posted back into #biomarker-research

## Tech Stack

| Tool | Role |
|------|------|
| Vectorize.io | Document ingestion, chunking and embedding pipeline |
| Pinecone | Vector storage and semantic retrieval |
| n8n | Agentic workflow orchestration |
| OpenAI | Embeddings and chat model |
| Slack | Conversational interface |
| Google Drive | Document source |

## Knowledge Base

The current knowledge base consists of 8 peer-reviewed oncology papers covering:

- Trop2 as a pan-cancer biomarker for ADC therapy selection
- Companion diagnostics co-development and regulatory approval timelines
- Evolution of oncology companion diagnostics from signal transduction to immuno-oncology
- PD-L1 and checkpoint inhibitor patient selection
- Biomarker applications in clinical trials
- Genomic biomarkers and clinical study design
- Predictive biomarkers across breast cancer therapies
- Prognostic enrichment strategies for clinical trials

Pipeline stats:
- Documents: 8
- Pages processed: 105
- Vectors stored: 343

## System Prompt

BiomarkerBot is constrained to answer only from the vector store. It will not use outside knowledge or hallucinate answers. If the answer cannot be found in the available research papers it says so clearly. Responses are written in plain English suitable for clinical operations, regulatory, and business development teams who may not have a scientific background.

## Demo

[Link to demo video]

## Setup

### Prerequisites

- n8n instance (cloud or self-hosted)
- Vectorize.io account
- Pinecone account
- OpenAI API key
- Slack workspace with a custom app

### Environment Variables

Create a `.env` file based on `.env.example`:

```
OPENAI_API_KEY=your_openai_api_key_here
PINECONE_API_KEY=your_pinecone_api_key_here
PINECONE_INDEX_NAME=biomarker
SLACK_BOT_TOKEN=your_slack_bot_token_here
SLACK_SIGNING_SECRET=your_slack_signing_secret_here
VECTORIZE_API_KEY=your_vectorize_api_key_here
```

### Steps

1. Upload your PDFs to a Google Drive folder
2. Create a RAG pipeline in Vectorize.io connected to that folder
3. Set chunking strategy to Paragraph, chunk size 500, overlap 50
4. Connect Vectorize to your Pinecone index using OpenAI v3 Small embeddings
5. Run the initial load to populate your vector database
6. Import the n8n workflow JSON from the `/workflow` folder
7. Add your credentials to each node in n8n
8. Create a Slack app with the required OAuth scopes
9. Add the Slack webhook URL to your n8n Slack Trigger node
10. Invite @BiomarkerBot to your chosen Slack channel
11. Test by mentioning @BiomarkerBot with a question

### Required Slack OAuth Scopes

```
app_mentions:read
channels:history
channels:read
chat:write
groups:history
im:history
im:read
```

## Repository Structure

```
BiomarkerRAG/
├── workflow/
│   └── Biomarker-bot_RAG.json
├── assets/
│   └── screenshots/
├── .env.example
├── .gitignore
└── README.md
```

## Known Limitations

- 8 papers is a small knowledge base and would need expansion for production use
- Figures, charts and tables embedded in PDFs are not retrieved, text only
- Built on free tier infrastructure and is not production scaled
- Bot responds to @ mentions only, not all messages in the channel

## Future Improvements

- Expand knowledge base with additional papers from PubMed
- Add metadata filtering to search by biomarker type or cancer indication
- Implement a feedback loop using Slack reactions to evaluate retrieval quality
- Add scheduled sync to automatically ingest new papers added to Google Drive

## Related Projects

[Autism Screening Classifier](link) — an ML classification model for early autism screening using behavioural data

## License

MIT
