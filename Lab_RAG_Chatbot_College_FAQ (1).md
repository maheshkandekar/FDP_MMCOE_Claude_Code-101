# Lab: Build a Local RAG Chatbot for College FAQs (100% Free, Runs Offline)

**Audience:** Engineering college professors
**Duration:** 90–120 minutes
**Prerequisites:** Basic Python familiarity, laptop with 8GB+ RAM, admin rights to install software
**Cost:** ₹0 — no API keys, no cloud subscription, no internet needed after setup

---

## Trainer Notes

This lab teaches Retrieval-Augmented Generation (RAG) — the technique behind most production chatbots — using only free, locally-run tools. Professors will end the session with a working chatbot that answers questions about their own college (admissions, fees, hostel rules, timings, exam policy) grounded in a document they wrote themselves, with **no hallucination risk from ungrounded facts** because every answer is retrieved from their FAQ file.

**Why local instead of an API-based approach:** Colleges often can't get procurement approval for paid API keys, and many campus networks block external API calls. Running everything locally (Ollama + ChromaDB) sidesteps both problems and lets professors deploy this on a lab machine or even a shared department PC.

**Conceptual arc to narrate as you teach:**
1. LLMs don't know your college — they weren't trained on your FAQ document
2. RAG = retrieve the relevant chunk of your document, then ask the LLM to answer *using only that chunk*
3. This is the same architecture used by ChatGPT plugins, enterprise chatbots, and Claude's own document Q&A — just running on a laptop instead of a data center

---

## Architecture Overview

```
Student Question
      │
      ▼
[1] Embed the question (sentence-transformers)
      │
      ▼
[2] Search ChromaDB for the 3 most similar FAQ chunks
      │
      ▼
[3] Stuff those chunks + question into a prompt
      │
      ▼
[4] Send prompt to local LLM (Ollama - Llama 3.2)
      │
      ▼
[5] LLM answers using ONLY the retrieved chunks
      │
      ▼
Answer shown in Streamlit chat UI
```

---

## Step 0: Install Prerequisites (do this before the session, or as Step 0 live)

**a) Install Python 3.10+** (skip if already installed)
```bash
python3 --version
```

**b) Install Ollama** (the free local LLM runner)

- Windows/Mac: download from https://ollama.com/download
- Linux:
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**c) Pull a small, fast open model** (runs fine on a laptop CPU, no GPU needed)
```bash
ollama pull llama3.2:3b
```
> This is a ~2GB download. If your lab has slow internet, do this the day before the session.

**d) Create a project folder and virtual environment**
```bash
mkdir college-rag-bot && cd college-rag-bot
python3 -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
```

**e) Install Python libraries**
```bash
pip install chromadb sentence-transformers streamlit ollama
```

---

## Step 1: Create the College FAQ Knowledge Base

Create a file called `faq_data.py` — this holds the "ground truth" the bot will answer from. In the live session, ask professors to **replace this with their own college's actual FAQs**.

```python
# faq_data.py
# Each entry becomes one retrievable chunk. Keep each answer self-contained
# (don't split one topic across two entries) — retrieval works best on
# focused, complete chunks.

FAQ_DATA = [
    {
        "question": "What is the admission process for B.Tech first year?",
        "answer": "Admission to B.Tech first year is through CET/JEE merit-based "
                   "centralized admission process (CAP) conducted by the state "
                   "DTE. Candidates must register on the official CAP portal, "
                   "fill in college preferences, and confirm the seat allotted "
                   "after document verification at the college admission cell."
    },
    {
        "question": "What documents are required at the time of admission?",
        "answer": "Candidates must carry: CET/JEE scorecard, 10th and 12th mark "
                   "sheets, school/college leaving certificate, domicile "
                   "certificate, caste certificate and validity (if applicable), "
                   "gap certificate (if any), and 6 passport-size photographs. "
                   "Original documents plus one photocopy set are required."
    },
    {
        "question": "What is the fee structure for the college?",
        "answer": "Tuition fees are as per the DTE-approved fee structure for "
                   "the respective branch, revised annually. Fees can be paid "
                   "online through the college ERP portal in two installments "
                   "per year. A fee receipt is generated instantly and is "
                   "required for library and hostel access."
    },
    {
        "question": "Is hostel accommodation available for students?",
        "answer": "Separate hostel facilities are available for boys and girls "
                   "on a first-come-first-served basis, subject to seat "
                   "availability. Hostel allotment forms are available at the "
                   "hostel office after admission confirmation. Mess facility "
                   "is compulsory for hostel residents."
    },
    {
        "question": "What are the college timings?",
        "answer": "Regular lectures run from 9:00 AM to 4:30 PM, Monday to "
                   "Saturday, with a lunch break from 12:30 PM to 1:15 PM. "
                   "The library remains open until 6:00 PM on working days."
    },
    {
        "question": "What is the attendance requirement to sit for exams?",
        "answer": "As per university regulations, students must maintain a "
                   "minimum of 75% attendance in each subject to be eligible "
                   "to appear for semester-end examinations. Students below "
                   "this threshold require a condonation application approved "
                   "by the Principal."
    },
    {
        "question": "How can students apply for scholarships?",
        "answer": "Students eligible for government scholarships (EBC, "
                   "minority, or merit-based) must apply through the state "
                   "scholarship portal (MahaDBT or equivalent) within the "
                   "notified window, and submit supporting documents to the "
                   "scholarship cell for verification."
    },
    {
        "question": "Who do I contact for placement-related queries?",
        "answer": "All placement-related queries should be directed to the "
                   "Training and Placement Officer (TPO) via the placement "
                   "cell email or during weekly placement office hours "
                   "announced on the notice board."
    },
]
```

**Trainer talking point:** Have each professor swap in 6–8 real FAQs from their own department before moving to Step 2. This makes the rest of the lab immediately relevant.

---

## Step 2: Build the Vector Store (Embed and Store the FAQs)

Create `build_index.py`:

```python
# build_index.py
import chromadb
from sentence_transformers import SentenceTransformer
from faq_data import FAQ_DATA

# Free, local embedding model (~80MB, downloads once)
print("Loading embedding model...")
embedder = SentenceTransformer("all-MiniLM-L6-v2")

# Persistent local vector database (stored in ./chroma_db folder)
client = chromadb.PersistentClient(path="./chroma_db")
collection = client.get_or_create_collection(name="college_faq")

documents, ids, metadatas = [], [], []

for i, item in enumerate(FAQ_DATA):
    # We embed Q+A together so the vector captures full context
    combined_text = f"Q: {item['question']}\nA: {item['answer']}"
    documents.append(combined_text)
    ids.append(f"faq-{i}")
    metadatas.append({"question": item["question"]})

print(f"Embedding {len(documents)} FAQ entries...")
embeddings = embedder.encode(documents).tolist()

collection.add(
    documents=documents,
    embeddings=embeddings,
    ids=ids,
    metadatas=metadatas
)

print("✅ Index built successfully. Stored in ./chroma_db")
```

Run it:
```bash
python build_index.py
```

**What just happened (explain to professors):** Each FAQ was converted into a list of ~384 numbers (a vector) that captures its *meaning*. Questions with similar meaning end up close together in this number-space, even if they use different words.

---

## Step 3: Build the Retrieval + Generation Logic

Create `rag_engine.py`:

```python
# rag_engine.py
import chromadb
from sentence_transformers import SentenceTransformer
import ollama

embedder = SentenceTransformer("all-MiniLM-L6-v2")
client = chromadb.PersistentClient(path="./chroma_db")
collection = client.get_collection(name="college_faq")

def retrieve_context(question, top_k=3):
    """Find the most relevant FAQ chunks for a student's question."""
    query_embedding = embedder.encode([question]).tolist()
    results = collection.query(
        query_embeddings=query_embedding,
        n_results=top_k
    )
    return results["documents"][0]  # list of matched text chunks

def generate_answer(question):
    """RAG: retrieve relevant chunks, then ask the local LLM to answer
    using only that retrieved context."""
    context_chunks = retrieve_context(question)
    context_text = "\n\n".join(context_chunks)

    prompt = f"""You are a helpful college helpdesk assistant.
Answer the student's question using ONLY the context below.
If the answer is not in the context, say "I don't have that information —
please contact the college office" instead of guessing.

Context:
{context_text}

Student question: {question}

Answer clearly and concisely:"""

    response = ollama.chat(
        model="llama3.2:3b",
        messages=[{"role": "user", "content": prompt}]
    )
    return response["message"]["content"], context_chunks

# Quick test when run directly
if __name__ == "__main__":
    q = "Do I need 75% attendance to give exams?"
    answer, sources = generate_answer(q)
    print("Q:", q)
    print("A:", answer)
```

Run it directly to test before building the UI:
```bash
python rag_engine.py
```

**Trainer checkpoint:** If this prints a sensible answer, the RAG pipeline is working end-to-end. Troubleshoot here before moving to the UI step.

---

## Step 4: Build the Chat Interface

Create `app.py`:

```python
# app.py
import streamlit as st
from rag_engine import generate_answer

st.set_page_config(page_title="College FAQ Assistant", page_icon="🎓")
st.title("🎓 College FAQ Assistant")
st.caption("Ask me about admissions, fees, hostel, timings, or exams.")

if "messages" not in st.session_state:
    st.session_state.messages = []

for msg in st.session_state.messages:
    with st.chat_message(msg["role"]):
        st.write(msg["content"])

user_question = st.chat_input("Ask your question...")

if user_question:
    st.session_state.messages.append({"role": "user", "content": user_question})
    with st.chat_message("user"):
        st.write(user_question)

    with st.chat_message("assistant"):
        with st.spinner("Looking that up..."):
            answer, sources = generate_answer(user_question)
            st.write(answer)
            with st.expander("📄 Source FAQ chunks used"):
                for s in sources:
                    st.text(s)

    st.session_state.messages.append({"role": "assistant", "content": answer})
```

Launch the chatbot:
```bash
streamlit run app.py
```

This opens a browser tab at `http://localhost:8501` with a working chat interface.

---

## Step 5: Test It

Have professors try these live in the session:

| Question | Expected behavior |
|---|---|
| "What documents do I need for admission?" | Answers from FAQ #2 |
| "Can I get a hostel room?" | Answers from FAQ #4 |
| "Do you offer a coding bootcamp?" | Should say it doesn't have that info (not hallucinate) |
| "What's the capital of India?" | Should decline — it's outside the FAQ scope |

The third and fourth rows are the most important demo moments — they prove the bot is **grounded** and won't invent answers.

---

## Lab Checklist

- [ ] Ollama installed and `llama3.2:3b` pulled
- [ ] Virtual environment created and libraries installed
- [ ] `faq_data.py` customized with real college FAQs
- [ ] `build_index.py` run successfully — `chroma_db` folder created
- [ ] `rag_engine.py` tested directly from terminal
- [ ] `streamlit run app.py` launches chatbot in browser
- [ ] Tested at least one "out of scope" question to confirm no hallucination

---

## Common Issues

| Problem | Fix |
|---|---|
| `ollama: command not found` | Restart terminal after install, or add Ollama to PATH |
| Very slow first response | Normal — first call loads the model into memory; later calls are faster |
| Answers ignore the FAQ content | Check `build_index.py` ran without errors and `chroma_db` folder exists |
| Out-of-memory on low-RAM laptop | Use `ollama pull llama3.2:1b` (smaller model) instead of 3b |

---

## Extension Ideas (Homework / Next Session)

1. Load FAQs from a CSV or Google Sheet instead of hardcoding them in Python
2. Add a feedback button so students can flag wrong answers
3. Swap `llama3.2:3b` for `phi3:mini` and compare answer quality
4. Deploy on a shared department PC so multiple students can access it over the campus LAN (`streamlit run app.py --server.address 0.0.0.0`)
5. Add multi-department FAQ collections (Admissions, Exam Cell, Placement) as separate ChromaDB collections
