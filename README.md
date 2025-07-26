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


---

### 1. **What method or library did you use to extract the text, and why? Did you face any formatting challenges with the PDF content?**

The system uses the **Default Data Loader** from `n8n-nodes-langchain.documentDefaultDataLoader`, which internally relies on **LangChain’s Document Loaders**. The source file is pulled from **Google Drive** using `googleDrive` integration. This method is reliable for parsing both English and Bangla PDF/DOCX content with minimal setup inside n8n.

**Why:** It is natively compatible with n8n and doesn't require external Python scripts.  
**Formatting Challenge:** While loading worked well, Bangla text may sometimes face alignment or OCR-related issues, which should be handled with preprocessing if required.

---

### 2. **What chunking strategy did you choose (e.g. paragraph-based, sentence-based, character limit)? Why do you think it works well for semantic retrieval?**

The workflow uses **Recursive Character Text Splitter**, a LangChain strategy applied via `textSplitterRecursiveCharacterTextSplitter`. It splits long content based on character limits with overlaps, preserving context.

**Why It Works:**  
- It keeps semantically meaningful text units.
- Works across multilingual (Bangla + English) input.
- Enables better vector representation for Pinecone retrieval.

---

### 3. **What embedding model did you use? Why did you choose it? How does it capture the meaning of the text?**

The embedding model used is **OpenAI’s `text-embedding-ada-002`**, accessed via the `Embeddings OpenAI` node in n8n.

**Why Chosen:**  
- It is multilingual and performs well in low-resource languages like Bangla.
- Fast, cost-effective, and integrates smoothly into LangChain + n8n.
- Converts chunks into dense vectors that capture semantic meaning across contexts.

---

### 4. **How are you comparing the query with your stored chunks? Why did you choose this similarity method and storage setup?**

**Similarity Method:** Cosine similarity  
**Vector Store:** Pinecone (`vectorStorePinecone`)

**Why This Setup:**  
- Pinecone offers high-speed approximate nearest neighbor (ANN) search.
- Cosine similarity is well-suited for comparing OpenAI embeddings.
- This combo ensures fast, semantically relevant retrievals even for Bangla queries.

---

### 5. **How do you ensure that the question and the document chunks are compared meaningfully? What would happen if the query is vague or missing context?**

- The **same embedding model** is used for both query and document chunks, ensuring semantic alignment.
- A **memory buffer** (via `memoryBufferWindow`) retains short-term context, improving multi-turn interactions.
- If a query is vague or unrelated, the AI Agent responds as follows:

> Bangla: “দুঃখিত, আমি এই প্রশ্নের নির্ভরযোগ্য উত্তর খুঁজে পাইনি।”  
> English: “Sorry, I could not find a reliable answer in the document.”

This prevents hallucinations or guessing.

---

### 6. **Do the results seem relevant? If not, what might improve them (e.g. better chunking, better embedding model, larger document)?**

✅ The results are accurate for both Bangla and English questions based on known test cases (`শুম্ভুনাথ`, `মামাকে`, `১৫ বছর` etc.).

**To improve further:**
- Use **sentence-paragraph hybrid** chunking for better granularity.
- Try newer multilingual models like **`bge-m3`, `instructor-xl`, or `LaBSE`** for Bangla-specific enhancements.
- Expand corpus with **HSC annotations or question banks**.

---


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
