# 📚 Multilingual HSC RAG Agent (Level-1 AI Engineer Assessment)

This project implements a multilingual Retrieval-Augmented Generation (RAG) system that answers Bengali and English questions based on the _HSC26 Bangla 1st Paper_ textbook. The solution is fully orchestrated in **n8n**, using **OpenAI**, **Pinecone**, and **LangChain** nodes to meet all assessment requirements.

You can directly avail this agent via:[10MS HSC Bangla Telegram Bot](https://t.me/tenms_hsc_bangla_bot)

---

## 🎯 Objective

To build a simple but effective RAG pipeline capable of:
- Accepting user queries in both **Bangla and English**
- Retrieving relevant document chunks from a vectorized PDF
- Generating grounded answers using an LLM (OpenAI)

---

## 🧩 Core Features

✅ Multilingual query handling (Bangla and English)  
✅ PDF document preprocessing and chunking  
✅ Embedding with OpenAI Embeddings  
✅ Vector storage using Pinecone  
✅ Retrieval-based answering (no hallucination)  
✅ Telegram bot interface  
✅ Short-term memory (session-based)  
✅ Example-based instruction logic for conversational clarity

---

## 🧠 Tools & Libraries Used

| Component              | Tool                        |
|------------------------|-----------------------------|
| LLM                    | OpenAI GPT-4o               |
| Embedding Model        | OpenAI Embeddings           |
| Vector DB              | Pinecone                    |
| Chunking Strategy      | RecursiveCharacterTextSplitter (via LangChain) |
| Orchestration Platform | n8n.io                      |
| Data Source            | HSC26 Bangla 1st Paper (PDF)|
| Bot Integration        | Telegram                    |

---

## ⚙️ Setup Guide

### 1. Clone the repo and install n8n (if self-hosted)
```bash
git clone https://github.com/your-username/hsc-rag-agent.git
cd hsc-rag-agent
```

### 2. Set up Pinecone and OpenAI credentials in `.env` or in n8n

- Pinecone API Key
- Pinecone Index: `banglahsc`
- OpenAI API Key
- Telegram Bot Token

### 3. Import the Workflows into n8n

- `Multilingual HSC RAG.json`: Runtime Agent for chat-based interaction  
- `pushData.json`: One-time run to preprocess, chunk, embed, and push data to Pinecone

### 4. Trigger the RAG Agent
- Via Telegram (`/start`)  
- Follow prompts to choose language and ask questions

---

## 📄 Sample Queries & Expected Output

| Question (User)                                     | Expected Answer |
|-----------------------------------------------------|------------------|
| অনুপমের ভাষায় সুপুরুষ কাকে বলা হয়েছে?              | শুম্ভুনাথ         |
| কাকে অনুপমের ভাগ্য দেবতা বলে উল্লেখ করা হয়েছে?     | মামাকে            |
| বিয়ের সময় কল্যাণীর প্রকৃত বয়স কত ছিল?           | ১৫ বছর            |
| Who is called a lucky god in Anupam’s words?       | His uncle (Mama) |

---

## 🔍 Evaluation Matrix

| Metric        | Implemented? | Notes |
|---------------|--------------|-------|
| Groundedness  | ✅           | All answers are based strictly on chunks retrieved from Pinecone |
| Relevance     | ✅           | Chunk retrieval is handled with semantic embedding + Pinecone vector matching |
| Cosine Similarity | ✅ (via vector tool) | Could be expanded via custom similarity scoring |
| Answer Accuracy | ✅ (Manual Verification) | All sample answers validated with context |
| Hallucination Guard | ✅ | System message enforces strict document grounding |

---

## ❓ Assessment Questions & Answers

### What method or library did you use to extract the text, and why?  
We used `LangChain Document Loader` in n8n to parse the PDF (via Google Drive). This ensures structured loading and supports direct piping into text splitters.

### Did you face any formatting challenges with the PDF content?  
Minor formatting challenges occurred due to Bengali font issues in certain PDF layouts. Text extraction from Google Docs version was more stable.

### What chunking strategy did you choose, and why?  
We used `RecursiveCharacterTextSplitter` with chunk size ~500 tokens and overlap. This approach balances semantic relevance and LLM context window constraints.

### What embedding model did you use, and why?  
`OpenAI Embeddings` due to its strong multilingual understanding and compatibility with Pinecone. It captures sentence-level semantics effectively.

### How are you comparing the query with your stored chunks?  
We embed the user query and use cosine similarity within Pinecone to retrieve the top-3 relevant chunks.

### Why did you choose this similarity method and storage setup?  
Cosine similarity with Pinecone ensures scalable and fast semantic retrieval, while OpenAI embeddings ensure language coverage (including Bengali).

### How do you ensure meaningful comparison between queries and document chunks?  
By using standardized embeddings for both, and retrieving semantically similar content. All LLM answers are gated through a vector tool call.

### What happens if the query is vague or missing context?  
The system is instructed to politely ask the user to clarify the question in their preferred language (Bangla or English).

### Do the results seem relevant? What might improve them?  
Yes, results match expected answers. Future improvements could include:
- Sentence-level chunking for better pinpointing
- Evaluation dashboard for automated QA scoring
- Bengali-native embedding model (e.g. `BanglaBERT`) for finer alignment

---

## 🚀 API (Optional Enhancement)

You can optionally add an n8n `Webhook` node to turn this into a lightweight REST API:

```
POST /webhook/rag-api
{
  "question": "অনুপমের ভাষায় সুপুরুষ কাকে বলা হয়েছে?"
}
→
{
  "answer": "শুম্ভুনাথ"
}
```

---

## 📁 Files

- `Multilingual HSC RAG.json` – Live runtime agent with Telegram interface
- `pushData.json` – PDF chunking and embedding to Pinecone
- `README.md` – Full documentation

---

## 🧪 Future Improvements

- Automated test evaluator for queries (score match vs expected)
- Full frontend UI (React or Streamlit)
- REST API support via Webhook Trigger in n8n
- Support other Bengali textbooks for RAG expansion
