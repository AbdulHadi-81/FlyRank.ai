# 🎥 3–5 Minute Video Demo Script & Recording Walkthrough

**Project**: AI Workflow Builder (Capstone FL-09)  
**Speaker**: Abdul Hadi  
**Target Duration**: 3 minutes 30 seconds to 4 minutes  
**Format**: Screen recording (Loom / OBS / QuickTime) showing live browser + terminal. **NO SLIDES!**

---

## ⚙️ Pre-Recording Setup Steps

Before pressing Record, run these two commands in separate terminal tabs:

1. **Start Next.js App**:
   ```bash
   cd c:\Users\Abdul Hadi\Desktop\Flyrank\Week-7\ai-workflow-builder
   npm run dev
   ```
   *(Ensure app is open at `http://localhost:3000`)*

2. **Start Inngest Dev Server**:
   ```bash
   npx inngest-cli@latest dev -u http://localhost:3000/api/inngest
   ```
   *(Ensure Inngest dashboard is open at `http://localhost:8288`)*

---

## 🎬 Minute-by-Minute Script & Action Plan

### ⏱️ Section 1: Introduction & What It Does (0:00 – 0:45)
**Screen Setup**: Show the browser window open to `http://localhost:3000` showing the React Flow canvas with a sample decision graph.

🗣️ **Say on camera / voiceover**:
> *"Hi everyone, I’m Abdul Hadi. This is the live demo for my FlyRank Backend AI Engineering capstone project: **AI Workflow Builder**.*
> 
> *In production systems, non-technical teams and ops engineers often need conditional branching logic, but hardcoding recursive nested `if/else` statements in backend code is rigid. AI Workflow Builder allows users to visually design decision trees on a canvas where every single node evaluates a question with AI and strictly routes execution down a YES or NO path."*

---

### ⏱️ Section 2: Live End-to-End Workflow Execution (0:45 – 1:45)
**Screen Action**: 
1. Click **Add Node** on the top toolbar. Type a prompt like: *"Is this customer asking for a refund?"*
2. Connect the **YES** green handle to Node 2 (*"Is the item under 30 days old?"*) and the **NO** red handle to Node 3 (*"Route to Support FAQ"*).
3. Click the **Star icon** on Node 1 to mark it as the Start node.
4. Click **Run Workflow** on the toolbar.

🗣️ **Say on camera / voiceover**:
> *"Let's see it run live end-to-end. I’ve configured a start node asking: 'Is this customer asking for a refund?'. Connected to its YES branch is a second node asking: 'Is the item under 30 days old?'.*
> 
> *When I click **Run Workflow**, the Next.js backend fires an event to Inngest. Watch the screen closely: as each step completes, the canvas highlights the active node and animated edge in sequence, while the execution panel on the right populates the live log traces step-by-step!"*

---

### ⏱️ Section 3: One Key Design Decision Explained (1:45 – 2:45)
**Screen Setup**: Switch tabs to show the Inngest Dev Server dashboard at `http://localhost:8288` showing the completed step-by-step run logs.

🗣️ **Say on camera / voiceover**:
> *"Now I want to explain a major architectural design decision I made: **using Inngest step functions instead of simple async loops**.*
> 
> *Early on, I considered running graph nodes inside a standard `while` loop in a Next.js API route. But LLM calls are non-deterministic—API keys get rate-limited, and network connections drop. If a 10-step loop fails on Step #5, standard scripts crash and lose all progress.*
> 
> *By using Inngest, every graph node runs in an isolated `step.run()` block. If an API call fails at Step 5, Inngest automatically retries only Step 5, preserving all previous state without re-running upstream steps."*

---

### ⏱️ Section 4: One Real Limitation Explained (2:45 – 3:30)
**Screen Setup**: Switch back to the canvas (`http://localhost:3000`) and point cursor to node connections.

🗣️ **Say on camera / voiceover**:
> *"To be completely honest about a current limitation: **the runner relies on a Directed Acyclic Graph (DAG) model**.*
> 
> *If a user accidentally connects Node A to Node B, and Node B back to Node A, creating a cycle, the system depends on a hard limit of 20 steps per run to prevent infinite API billing loops. In a future version, I plan to build stateful decay handles to handle cyclical loops natively."*

---

### ⏱️ Section 5: Wrap Up & AI Transparency Disclosure (3:30 – 3:50)
**Screen Setup**: Show the `README.md` or portfolio website.

🗣️ **Say on camera / voiceover**:
> *"Regarding AI transparency in my workflow: I built this using Claude and Antigravity for React Flow boilerplate and Inngest schemas, while personally writing and verifying the strict boolean parser, edge handles, and rate-limit retries.*
> 
> *Thank you for watching, and all setup code and eval benchmarks are available in the repository README!"*

---

## 🎬 Checkoff List After Recording

- [ ] Video duration is between **3 and 5 minutes**.
- [ ] Shows **live app execution**, not slides.
- [ ] Explained **1 design decision** (Inngest step durability).
- [ ] Explained **1 limitation** (DAG graph cycle limit).
- [ ] Copy video link (Loom/YouTube/Vimeo) into your showcase thread post.
