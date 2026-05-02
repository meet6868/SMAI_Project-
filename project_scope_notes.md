# Phase 1: Requirement Lock and Scope Freeze

## Source Used
- question.txt

## Goal
- Convert the assignment statement into a fixed build scope.

## Mandatory Build Features (Locked)
1. Build a chatbot for Indian voter ID (EPIC) services.
2. Chatbot must answer process questions like: How do I apply for X?
3. System must retrieve from official PDF documents.
4. Every answer must show source PDF citations.
5. Solution type must be RAG (Retrieval-Augmented Generation).
6. No model training is allowed.
7. Build chat UI in Streamlit using st.chat_input.

## Mandatory Technical Base (Locked)
1. Embeddings: sentence-transformers/all-MiniLM-L6-v2.
2. Vector store: chromadb or faiss-cpu.
3. LLM API: Google AI Studio (Gemini 1.5 Flash) or Groq (Llama-3 free).

## Mandatory Submission Items (Locked)
1. Public GitHub repository with:
   - README.md
   - requirements.txt
   - training notebook
   - app code
2. Live demo on HuggingFace Spaces or Streamlit Community Cloud.
   - Fallback: recorded screen demo of 2 minutes or less.
3. Technical report PDF with sections:
   - Introduction
   - Data
   - Method
   - Results
   - Limitations
   - References
   - Ablation where necessary
4. One-slide pitch in single PNG or PDF.

## Optional Feature (Separated)
1. Talk in Hindi toggle.

## Scope Freeze Rules
1. No assumptions beyond question.txt.
2. No extra problem statements.
3. No training-based pipeline.
4. No non-PDF source as primary knowledge base.
5. No change to required deliverable list.

## Output of Phase 1
- This fixed checklist document is the requirement lock used by later phases.
