# RAG Evaluation Pipeline with RAGAS and Observability

🚀 **Overview**

This project implements an end-to-end Retrieval-Augmented Generation (RAG) evaluation pipeline using:

- **LangChain** for building the RAG system
- **RAGAS** for evaluation
- **ChromaDB** for vector storage
- **OpenAI** models for generation and embeddings
- **Langfuse** for observability and monitoring

The system automatically generates test datasets, evaluates model performance, and logs metrics for production-level analysis.

---

## 🧠 Problem Statement

Evaluating LLM-based RAG systems is challenging because:

- Outputs are non-deterministic
- Ground truth is often unavailable
- Hallucinations are hard to detect
- Retrieval quality directly impacts performance

This project solves these by using **reference-free evaluation metrics** and **synthetic dataset generation**.

---

## 🏗️ Architecture

```
Documents → Chunking → Embeddings → Vector DB → Retriever
         → RAG Pipeline → Generated Answers
         → RAGAS Evaluation → Metrics → Langfuse Monitoring
```

---

## ⚙️ Tech Stack

| Tool | Purpose |
|------|---------|
| Python | Core language |
| LangChain | RAG orchestration |
| RAGAS | Evaluation framework |
| OpenAI (GPT + Embeddings) | Generation & embeddings |
| ChromaDB | Vector database |
| Langfuse | Observability & monitoring |
| Pandas, Seaborn | Data analysis & visualization |

---

## 🔄 Pipeline Steps

### 1. Document Processing
- Load raw text documents
- Split into overlapping chunks
- Add metadata for traceability

### 2. Test Dataset Generation
- Automatically generate evaluation questions using LLMs
- Supports:
  - Simple questions
  - Reasoning-based questions
  - Multi-context questions

### 3. RAG System
- Convert chunks into embeddings
- Store in vector database (ChromaDB)
- Retrieve relevant documents
- Generate answers using LLM

### 4. Evaluation (RAGAS)

| Metric | Purpose |
|--------|---------|
| Context Relevancy | Are retrieved documents relevant? |
| Context Precision | How much retrieved context is useful? |
| Context Recall | Did we retrieve all necessary information? |
| Faithfulness | Is the answer grounded in context? |
| Answer Relevancy | Does the answer address the question? |

### 5. Visualization
- Heatmaps to analyze performance
- Identify weak components in the pipeline

### 6. Observability (Langfuse)
- Track evaluation runs as traces
- Log metric scores
- Monitor system performance in production

---

## 🔥 Key Features

- ✅ Synthetic test dataset generation
- ✅ End-to-end RAG evaluation
- ✅ Reference-free metrics (no labeled data required)
- ✅ Production-grade observability
- ✅ Modular and extensible pipeline

---

## 🧪 How to Run

**Install dependencies:**

```bash
pip install langchain ragas chromadb openai langfuse seaborn pandas
```

**Set environment variables:**

```bash
export OPENAI_API_KEY=your_key
export LANGFUSE_PUBLIC_KEY=your_key
export LANGFUSE_SECRET_KEY=your_key
```

**Run the notebook:**

```bash
jupyter notebook ragas.ipynb
```

---

## ⚠️ Limitations

- Small test dataset size (default: 10)
- No advanced error analysis
- Requires OpenAI API access
- Metrics depend on LLM-based evaluation

---

## 🛠️ Future Improvements

- Add DeepEval for hybrid evaluation
- Increase dataset size for robustness
- Implement failure case analysis
- Compare different retrievers and embeddings
- Add latency and cost tracking

---

## 📌 Use Cases

- Evaluating RAG-based chatbots
- Debugging hallucinations
- Benchmarking retrieval systems
- Monitoring LLM applications in production

---

## 🎯 Learning Outcomes

This project demonstrates:

- How to evaluate LLM systems beyond accuracy
- How to design evaluation pipelines
- How to integrate observability into AI systems
- How to debug RAG failures using metrics

---

## 🤝 Contributing

Feel free to fork the repo and improve:

- Evaluation strategies
- Observability integrations
- Dataset generation methods

---

## 📜 License

MIT License
