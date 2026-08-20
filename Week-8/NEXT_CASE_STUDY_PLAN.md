# Plan to Keep Building: Portfolio Case Study Expansion Guide

**Developer**: Abdul Hadi  
**Portfolio Repository**: [`AbdulHadi-81.github.io`](file:///c:/Users/Abdul%20Hadi/Desktop/AbdulHadi-81.github.io/index.html)  
**Target Completion Date**: September 15, 2026  

---

## 🎯 1. Target Destination & File Location

Your next case study will be published directly to your live personal portfolio website:

- **Primary Destination Page**: `c:/Users/Abdul Hadi/Desktop/AbdulHadi-81.github.io/work.html` (Full Case Study View)
- **Home Highlights Cards**: `c:/Users/Abdul Hadi/Desktop/AbdulHadi-81.github.io/index.html` (Featured Work Grid)

---

## 🔁 2. The 3-Beat Case Study Addition Workflow

Whenever adding a new project or case study, follow this exact 3-beat narrative shape (established in Week 2):

```
+-------------------+      +-------------------+      +-------------------+
|   1. THE PROBLEM  | ---> |  2. WHAT YOU DID  | ---> | 3. WHAT CAME OF IT|
|  (Messy Real Input|      | (Architecture, AI |      | (Verifiable Metric|
|   & Bottlenecks)  |      |   & Stack Setup)  |      |   & Outcome)      |
+-------------------+      +-------------------+      +-------------------+
```

### Step 1: The Problem Beat
- State the explicit friction, non-deterministic output, or operational cost.
- *Template*: *"Engineers spending 4+ hours navigating multi-file legacy repositories manually without semantic code awareness."*

### Step 2: What You Did Beat
- Outline the technical stack, architecture decisions, and durability patterns.
- Mention specific tools used (e.g., Next.js App Router, FastAPI, Inngest durable steps, ChromaDB/Pinecone vector storage, Mistral/OpenAI API).
- *Template*: *"Built an Autonomous RAG Code Search Engine utilizing AST chunking, local vector embeddings, and an Inngest durable retrieval pipeline with isolated retry boundaries."*

### Step 3: What Came of It Beat
- Provide empirical numbers, benchmarks, or tangible user outcomes.
- *Template*: *"Achieved 94.2% precision on multi-file code query benchmarks, reduced query response latency to 340ms, and verified 100% step recovery on rate-limit exceptions."*

---

## 🚀 3. Named Next Piece of Work

- **Project Title**: **Autonomous RAG & Semantic Codebase Search Engine**
- **Core Capabilities**:
  1. AST-based code parser chunking functions and docstrings.
  2. Vector storage with semantic similarity retrieval.
  3. Inngest durable step pipeline for asynchronous indexing & embedding retries.
  4. React UI with live citation links pointing directly to exact source lines.
- **Target Completion Date**: September 15, 2026

---

## 🔔 4. Concrete Reminder Setup & Evidence

To ensure the portfolio continues to evolve and never goes stale:

- **Calendar Event Nudge**: Set for **September 15, 2026 at 10:00 AM PKT**.
- **Event Title**: `[FlyRank AI Portfolio] Build & Publish Next Case Study: RAG Code Search Engine`
- **Companion File**: Generated `reminder_next_case_study.ics` calendar file in the workspace directory (importable into Google Calendar, Outlook, or Apple Calendar).

---

## 🧠 5. Preserved Claude Project / AI Assistant Context

To ensure future case study updates require only a short conversation rather than rebuilding context, save this prompt snippet inside your **Claude Project Custom Instructions / System Prompt**:

```yaml
Role: AI Assistant for Abdul Hadi (Backend & AI Engineer)
Voice & Tone: Engineering-first, concise, authoritative, empirical metrics-driven.
Identity Kit:
  - Developer: Abdul Hadi
  - Portfolio URL: https://haditci.site/
  - Tech Stack: Next.js, React, FastAPI, Python, Inngest, Tailwind CSS, OpenAI / Mistral APIs.
  - Case Study Blueprint:
      Beat 1 (Problem): Real-world operational friction & messy input.
      Beat 2 (What I Did): Architectural design, durable functions, LLM prompt guardrails.
      Beat 3 (Outcome): Empirical benchmarks (% accuracy, latency ms, zero-failure retries).
Instruction:
  When asked to format a new project into a case study, produce the HTML card for index.html, 
  the detailed section for work.html, and a 3-beat markdown summary adhering strictly to this blueprint.
```
