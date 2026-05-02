# Voter ID / EPIC Assistant: Phase-Wise Execution Guide

## 1) Problem Statement (from question file)

Build a chatbot for Indian voter ID (EPIC) services that can answer questions like:
- "How do I apply for X?"

The chatbot must retrieve answers from official PDF documents and show source PDF citations in every answer.

Important scope from the question:
- Build a RAG pipeline (Retrieval-Augmented Generation).
- No model training.
- Use free embeddings and a free LLM API.
- Build chat UI in Streamlit using `st.chat_input`.
- Optional: "Talk in Hindi" toggle.

---

## 2) Data (only from question file)

### What data is allowed
- Public official PDF documentation related to Indian voter ID / EPIC services.

### Data role in this project
- PDFs are the knowledge source.
- Chatbot answers must come from these PDFs through retrieval.
- Citations must point back to these PDFs.

---

## 3) Common Base (fixed technical choices from question file)

Use these components:
- Embeddings: `sentence-transformers/all-MiniLM-L6-v2` (free, CPU)
- Vector store: `chromadb` or `faiss-cpu`
- LLM API: free Google AI Studio (Gemini 1.5 Flash) or Groq (free Llama-3)

---

## 4) End Deliverables Required

You must hand in:
1. Public GitHub repository with:
   - `README.md`
   - `requirements.txt`
   - training notebook
   - app code
2. Live demo:
   - HuggingFace Spaces (free) or Streamlit Community Cloud (free)
   - If hosting fails: recorded screen demo (maximum 2 minutes)
3. Technical report PDF with sections:
   - Introduction
   - Data
   - Method
   - Results
   - Limitations
   - References
   - Ablation (where necessary)
4. One-slide pitch (single PNG/PDF)

---

## 5) Phase-Wise Solution Plan (Input, Rules, Process, Output)

## Phase 1: Requirement Lock and Scope Freeze

### Goal
Convert the problem statement into exact build scope.

### Input
- Assignment text in `question.txt`

### Rules
- No model training.
- Must be RAG.
- Must use official PDFs as source.
- Every answer must show source citation.
- Streamlit chat interface is required.

### How to do
1. Read the assignment line by line.
2. List mandatory features and mandatory submissions.
3. Mark optional features separately (Hindi toggle).
4. Freeze scope so implementation does not drift.

### Output
- A fixed requirement checklist used by all later phases.

---

## Phase 2: Project Structure and Repository Setup

### Goal
Create a clean repository that matches submission needs.

### Input
- Requirement checklist from Phase 1.

### Rules
- Repository must be public.
- Must include `README.md`, `requirements.txt`, training notebook, app code.
- Structure should be easy to run and review.

### How to do
1. Create clear folders for app, data handling, and notebook.
2. Add `requirements.txt` with exact packages used.
3. Write README with setup and run instructions.
4. Keep code and notebook consistent (same pipeline logic).

### Output
- Submission-ready repository skeleton with all required files.

---

## Phase 3: PDF Data Ingestion

### Goal
Load official PDF documents so text can be processed.

### Input
- Official voter ID/EPIC PDFs.

### Rules
- Source must be official public PDFs.
- Preserve source identity (file name, page number) for citations.
- Do not alter meaning of text during extraction.

### How to do
1. Read each PDF.
2. Extract text page by page.
3. Store metadata for each text unit:
   - PDF file name
   - page number
4. Remove only extraction noise if needed (extra spaces, broken newlines).

### Output
- Clean extracted text units with metadata for citation.

---

## Phase 4: Text Chunking for Retrieval

### Goal
Split extracted text into searchable chunks.

### Input
- Clean text units + metadata from Phase 3.

### Rules
- Chunk size must be large enough for context and small enough for precise retrieval.
- Each chunk must keep source metadata.
- No chunk should lose traceability to original PDF/page.

### How to do
1. Break text into chunks using a consistent chunk policy.
2. Keep overlap between chunks to avoid context loss at boundaries.
3. Attach metadata to every chunk.
4. Validate a sample of chunks manually for readability.

### Output
- Chunked corpus where each chunk has text + source metadata.

---

## Phase 5: Embedding Generation

### Goal
Convert text chunks into vectors for semantic search.

### Input
- Chunked corpus from Phase 4.
- Embedding model: `sentence-transformers/all-MiniLM-L6-v2`.

### Rules
- Use the model specified in the assignment base.
- Embedding generation must be deterministic and reproducible.
- Keep mapping: chunk ID -> embedding -> metadata.

### How to do
1. Load embedding model.
2. Pass every chunk text through model.
3. Save vectors with chunk ID and metadata.
4. Verify vector count equals chunk count.

### Output
- Embedded chunk dataset ready for vector indexing.

---

## Phase 6: Vector Store Indexing

### Goal
Create fast semantic retrieval index.

### Input
- Embedded chunks from Phase 5.
- Vector store choice: `chromadb` or `faiss-cpu`.

### Rules
- Use one of the two allowed stores from assignment.
- Index must preserve chunk metadata for citations.
- Retrieval must return top relevant chunks for any query.

### How to do
1. Initialize vector store.
2. Insert embeddings and metadata.
3. Build index.
4. Run trial queries to ensure expected chunk retrieval.

### Output
- Working vector index that returns relevant chunks + metadata.

---

## Phase 7: Retrieval Pipeline

### Goal
Given a user query, fetch most relevant chunks from PDFs.

### Input
- User question.
- Vector index from Phase 6.

### Rules
- Retrieval must happen before answer generation.
- Retrieved chunks should come from official PDF corpus only.
- Keep source references for final citation display.

### How to do
1. Convert query into embedding.
2. Search vector store for top-k similar chunks.
3. Collect chunk text + file/page metadata.
4. Pass only retrieved context to generation layer.

### Output
- A context package: top retrieved evidence chunks with citations.

---

## Phase 8: LLM Answer Generation with Citations

### Goal
Generate final answer from retrieved context, with source citations.

### Input
- User question.
- Retrieved context package from Phase 7.
- LLM API: Gemini 1.5 Flash or Groq Llama-3 free.

### Rules
- Answer must be grounded in retrieved PDF context.
- Every answer must display source PDF citation(s).
- Do not answer from outside provided retrieved context.

### How to do
1. Build prompt with:
   - user question
   - retrieved chunk text
   - citation metadata
   - instruction to cite sources
2. Call selected free LLM API.
3. Format answer and attach citations clearly.
4. If context is insufficient, respond safely using available evidence only.

### Output
- Final chatbot response text + explicit source citation list.

---

## Phase 9: Streamlit Chat Application

### Goal
Provide interactive chat UI for end users.

### Input
- Retrieval + generation pipeline from Phases 7 and 8.

### Rules
- Use Streamlit chat interface with `st.chat_input`.
- Each response must show citation(s).
- Hindi toggle is optional (as per assignment).

### How to do
1. Build chat layout with Streamlit.
2. Capture user query through `st.chat_input`.
3. Trigger retrieval and generation pipeline.
4. Render answer text and source citations.
5. Add optional toggle for "Talk in Hindi" mode.

### Output
- Functional Streamlit app where users ask questions and get cited answers.

---

## Phase 10: Results and Evaluation

### Goal
Show how well the system answers the assignment-style queries.

### Input
- Running app/pipeline.
- A set of representative user questions (about voter services in PDFs).

### Rules
- Evaluation and analysis must be your own work.
- Report should focus on relevance and citation correctness.
- Keep evidence-driven examples.

### How to do
1. Ask multiple realistic questions.
2. Check whether answer is useful and grounded.
3. Verify citation points to correct PDF evidence.
4. Document strengths and failure cases.

### Output
- A clear results section with grounded examples and observed behavior.

---

## Phase 11: Ablation (Where Necessary)

### Goal
Compare key pipeline choices and show impact.

### Input
- Baseline RAG pipeline.
- One changed setting at a time.

### Rules
- Include this section where necessary (as required).
- Change one factor per comparison.
- Report differences in answer quality/citation quality.

### How to do
1. Keep one baseline configuration.
2. Change one component/setting.
3. Re-run same evaluation questions.
4. Note impact clearly.

### Output
- Ablation section for report with controlled comparisons.

---

## Phase 12: Deployment and Demo

### Goal
Deliver a working public demo.

### Input
- Final Streamlit app.

### Rules
- Preferred free hosting: HuggingFace Spaces or Streamlit Community Cloud.
- If hosting fails, provide <=2 minute recorded screen demo.

### How to do
1. Prepare app startup command and dependency file.
2. Deploy on one approved platform.
3. Test chat, retrieval, and citations in deployed version.
4. If deployment fails, record concise demo video.

### Output
- Live URL or compliant fallback demo recording.

---

## Phase 13: Technical Report PDF

### Goal
Create complete report in required format.

### Input
- Final system, evaluation notes, ablation observations.

### Rules
Report must include sections:
- Introduction
- Data
- Method
- Results
- Limitations
- References
- Ablation (where necessary)

### How to do
1. Explain problem and motivation.
2. Describe data source and preprocessing.
3. Describe RAG method end to end.
4. Present results and grounded examples.
5. Add limitations honestly.
6. Add references and acknowledgements for LLM usage.

### Output
- Final technical report PDF meeting required section structure.

---

## Phase 14: One-Slide Pitch

### Goal
Create one concise slide for public sharing.

### Input
- Core project summary and key results.

### Rules
- Exactly one slide.
- Output format: PNG or PDF.
- Must be understandable quickly.

### How to do
1. Add project title and one-line problem statement.
2. Show simple pipeline flow (PDF -> Retrieval -> LLM -> Cited Answer).
3. Add one example Q/A with citation.
4. Add short impact statement.

### Output
- Single pitch slide file (PNG/PDF).

---

## 6) Phase Dependency Chain (End-to-End Flow)

1. Phase 1 defines scope.
2. Phase 2 sets repository and tooling base.
3. Phase 3 and 4 prepare retrieval-ready text.
4. Phase 5 and 6 build semantic search backbone.
5. Phase 7 and 8 produce grounded answers with citations.
6. Phase 9 exposes the system to users.
7. Phase 10 and 11 provide evidence and analysis.
8. Phase 12, 13, 14 complete mandatory deliverables.

---

## 7) Hard Rules Summary (must not break)

- No model training.
- Use RAG from official PDFs.
- Use assignment-approved embedding, vector DB, and free LLM options.
- Streamlit chat with `st.chat_input`.
- Every answer must show source citation.
- Submit all four required deliverables in required formats.
- Evaluation and analysis must be your own.
- Mention LLM tools used in Acknowledgements.
