# n8n-rag-chatbot
A RAG chatbot built in n8n that syncs a Google Drive folder into a searchable knowledge base and answers questions about the files using OpenAI embeddings and Groq.
# n8n RAG Chatbot

A Retrieval-Augmented Generation (RAG) chatbot built in **n8n** that automatically syncs files from a Google Drive folder into a searchable knowledge base, and answers user questions about their content through a chat agent.

## 🧠 How It Works

**Ingestion Pipeline**
- Google Drive Trigger (fires on file created / file updated)
- Extracts file metadata via a JavaScript code node
- Downloads the file
- Splits the text into chunks (Recursive Character Text Splitter)
- Generates embeddings using OpenAI
- Stores the vectors in a Vector Store (Insert Mode)

**Retrieval Pipeline**
- Chat message received
- AI Agent embeds the user's question (same OpenAI embedding model)
- Searches the Vector Store for relevant chunks (Retrieve Mode)
- Combines retrieved context with conversation memory
- Groq LLM generates the final answer

## 🐞 Key Bug Fixed

The ingestion side originally used **OpenAI embeddings**, while the retrieval side used **Google Gemini embeddings**. Since different embedding models produce incompatible vector spaces, this silently broke similarity search — the agent could never find relevant content. Fixed by unifying both sides to use the same OpenAI embedding model.

## 🛠️ Tools Used

- **n8n** — workflow automation / orchestration
- **OpenAI Embeddings API** — text-embedding-3-small
- **Groq** — LLM for generating chat responses
- **Google Drive API** — file source / trigger
- **n8n Simple Vector Store** — in-memory vector storage (demo/testing)

## 🐍 Bonus: Python/Colab Version

As an additional exercise, the same RAG pipeline was rebuilt in **Google Colab** using plain Python, to understand each step at the code level:

- Google Drive API for file listing & download
- LangChain's `RecursiveCharacterTextSplitter` for chunking
- Local embeddings via `sentence-transformers` (no external API key required)
- A custom in-memory vector store class (cosine similarity search)
- Groq API for the chat response generation

This version mirrors the n8n workflow 1:1, demonstrating the same architecture with full code-level control.

## ⚠️ Notes / Limitations

- The n8n version uses **Simple Vector Store**, which is in-memory only — data resets when the n8n session restarts. A production deployment would use  apersistent vector database (e.g. Pinecone).
- This project was built as a learning exercise to understand RAG architecture, embeddings, and n8n's AI Agent nodes.
