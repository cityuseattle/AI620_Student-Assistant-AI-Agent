<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# [https://www.youtube.com/@MicrosoftAzure/search?query=foundry](https://www.youtube.com/@MicrosoftAzure/search?query=foundry)

[https://learn.microsoft.com/en-us/azure/foundry-classic/agents/quickstart?pivots=ai-foundry-portal](https://learn.microsoft.com/en-us/azure/foundry-classic/agents/quickstart?pivots=ai-foundry-portal)

[https://github.com/microsoft-foundry/ai-tutorials](https://github.com/microsoft-foundry/ai-tutorials)

>> The attached document is my project proposal and I want you to go through the idea and the references and give me a proper plan on how to build this project and I want you to understand that we are 2 people and we have 5 weeks to build this and we are thinking to make this in Azure, AWS (free tires) or with a Local Model, what would you suggest and why, give me the whole game plan for this project.

I now have a complete picture of your project. Let me give you a full game plan.

***

# CityU AI Student Assistant — 5-Week Game Plan

Your project is a **CityU Student Assistant AI Agent** built with: an LLM, RAG (for document search), a Vector DB, and an MCP server for tool/database connectivity. Here's everything you need to know to build this in 5 weeks as a 2-person team.[^1_1]

***

## Platform Recommendation: Local Model First, Azure for Demo

With only 2 people and 5 weeks, **start local, deploy to Azure for the final demo**. Here's why:


| Option | Pros | Cons | Verdict |
| :-- | :-- | :-- | :-- |
| **Local (Ollama + LLaMA 3)** | Zero cost, full control, fast iteration, no API limits | Can't share publicly, no serverless auto-scale | ✅ **Best for weeks 1–4** |
| **Azure AI Foundry (free tier)** | GPT-4o-mini is cheap, serverless functions, managed infra | Free tier has token limits, Azure complexity | ✅ **Best for week 5 demo** |
| **AWS Free Tier (Bedrock)** | Familiar to you (you've used Rekognition/S3), Lambda | Bedrock has very limited free access; Claude/Titan cost money quickly | ⚠️ Riskier — Bedrock models are NOT free |

**Recommended stack:** Ollama (local LLM) → LangChain → ChromaDB (vector DB) → FastAPI (MCP-style server) → Streamlit or simple HTML UI. For final submission, swap the local LLM call with Azure OpenAI (GPT-4o-mini) using free credits.[^1_1]

***

## Architecture Overview

```
User Query
    ↓
[Frontend: Streamlit or HTML/JS]
    ↓
[FastAPI Backend — your "MCP Server"]
    ↓              ↓
[RAG Module]   [Tool Calls (course info, schedule lookup)]
    ↓
[ChromaDB Vector DB — CityU docs, syllabi, FAQs]
    ↓
[LLM: Ollama/LLaMA 3 locally → GPT-4o-mini on Azure for demo]
    ↓
Response
```


***

## 5-Week Sprint Plan

### Week 1 — Foundation \& Data Ingestion (Both work together)

- **Person A**: Set up the repo, install Ollama + pull `llama3` model, set up Python virtualenv, install LangChain, ChromaDB, FastAPI
- **Person B**: Scrape/collect CityU data — course catalog pages, FAQ PDFs, degree requirement docs (these become your RAG knowledge base)
- **Joint**: Chunk and embed the documents into ChromaDB using `langchain-community` + `HuggingFaceEmbeddings` (free, no API key needed locally)
- **Milestone**: ChromaDB populated, test a raw similarity search query


### Week 2 — RAG Pipeline + Basic Agent

- **Person A**: Build the RAG chain with LangChain (`RetrievalQA` or `ConversationalRetrievalChain`), connect to ChromaDB retriever, hook into local Ollama LLM
- **Person B**: Build the FastAPI backend with endpoints: `POST /chat`, `GET /health`, design the "MCP server" as a tool-calling wrapper (LangChain tools for course lookup, semester info)
- **Milestone**: You can type a question like "What courses does CityU offer in AI?" and get a RAG-grounded answer via API


### Week 3 — Tool Integration + MCP Server Pattern

- **Person A**: Add LangChain tools — at minimum: (1) RAG retriever tool, (2) a mock "course schedule" tool that queries a JSON/SQLite database
- **Person B**: Build a minimal SQLite or JSON database with sample CityU course data (course codes, credits, prereqs, professors), connect it to the tool
- **Joint**: Wire tools into a LangChain Agent (`AgentExecutor` with `create_react_agent`) so the LLM can decide which tool to call
- **Milestone**: Agent correctly routes "What are the prereqs for AI620?" to the DB tool vs. "What is RAG?" to the RAG retriever


### Week 4 — Frontend UI + Memory/Context

- **Person A**: Build a Streamlit chat UI (fastest option) with chat history, source citation display (show which document the answer came from), and a simple sidebar showing "Agent is thinking..."
- **Person B**: Add conversation memory (LangChain `ConversationBufferWindowMemory`), handle multi-turn questions like "What about its prereqs?" after asking about a course
- **Joint**: End-to-end integration test, fix hallucination issues, add a system prompt that scopes the agent to CityU-only topics
- **Milestone**: Full working local demo — chat UI → FastAPI → Agent → RAG/Tools → LLM response


### Week 5 — Cloud Deployment + Polish

- **Person A**: Deploy FastAPI to **Azure Container Apps** (free tier) or **Azure Functions** using Docker. Swap local Ollama with **Azure OpenAI GPT-4o-mini** (students get \$100 Azure credits via Azure for Students — completely free)
- **Person B**: Deploy ChromaDB to a persistent volume on Azure, OR switch to **Azure AI Search** (free tier: 50MB index) as your vector store for a more "Azure-native" demo
- **Joint**: Write the final report, record a demo video, document the architecture, prep the GitHub repo README
- **Milestone**: Public-facing demo URL, project submitted

***

## Key Tools \& Libraries (All Free)

| Component | Tool | Why |
| :-- | :-- | :-- |
| Local LLM | **Ollama + LLaMA 3.1 8B** | Free, fast on CPU/GPU, great for testing |
| Cloud LLM | **Azure OpenAI GPT-4o-mini** | Free with Azure for Students credits |
| Orchestration | **LangChain** | Matches your keywords exactly [^1_1] |
| Vector DB | **ChromaDB** | Easiest local setup, 0 cost |
| Embeddings | **sentence-transformers** (`all-MiniLM-L6-v2`) | Free, runs locally |
| Backend API | **FastAPI** | Your "MCP server" layer |
| Frontend | **Streamlit** | Chat UI in ~50 lines of Python |
| Database | **SQLite** | For structured course data |
| Cloud deploy | **Azure Container Apps** | Free tier, maps to your proposal |


***

## Division of Labor (2 People)

Since you're 2 people, split by **layer, not by week**:

- **Person 1 (Backend/AI)**: LLM integration, LangChain agent, RAG pipeline, tool design — this is the AI engineering track
- **Person 2 (Data/Infrastructure)**: Data collection, vector DB setup, FastAPI routes, cloud deployment, UI — this is the systems/infra track

Both converge for integration testing in Weeks 3–4, and both contribute to documentation in Week 5.

***

## Critical Advice

1. **Don't over-scope.** Your proposal wisely says "scope down to just building the agent as first milestone"  — stick to that. A polished 3-tool agent beats a half-broken 10-tool one.[^1_1]
2. **Use Azure for Students** — go to `azure.microsoft.com/en-us/free/students` and claim your \$100 credit. This makes the Azure path genuinely free for your demo week.
3. **Mock your data early.** CityU may not have public APIs. Create a `cityu_courses.json` with 20–30 courses manually — enough to make the demo believable.
4. **RAG quality > model quality.** A well-chunked, well-retrieved context with LLaMA 3 8B will outperform a poorly retrieved context with GPT-4o. Invest time in chunk size (300–500 tokens) and metadata tagging.
5. **The MCP pattern** from your proposal  is essentially the LangChain `AgentExecutor` + tools pattern. You don't need a dedicated MCP server library — the FastAPI backend serving tool endpoints *is* your MCP server.[^1_1]

<div align="center">⁂</div>

[^1_1]: TP01_T05_AI620_Shagun_Zandashia_Sai.docx

