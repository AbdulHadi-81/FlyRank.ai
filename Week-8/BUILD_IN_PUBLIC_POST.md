# 🚀 Shipped My Capstone: AI Workflow Builder (Backend AI Engineering Track)

Over the past 8 weeks in the FlyRank Backend AI Engineering track, I transitioned from writing basic backend API endpoints to building a complete visual graph execution engine: **AI Workflow Builder**.

Here is the story of what I built, the major architectural decision I made, and the key limitation I ran into.

---

## 💡 What It Does

**AI Workflow Builder** is a visual decision pipeline engine built with **Next.js 15**, **React Flow**, **Inngest**, and the **Mistral / OpenAI Chat Completions API**. 

Instead of writing complex nested `if/else` logic in code, non-technical team members or ops engineers can visually drag decision nodes onto a canvas, write questions like *"Is this request high priority?"*, and connect **YES** (green) and **NO** (red) decision handles to route execution.

---

## 🏗️ 1 Real Design Decision: Durable Step Functions Over Async Loops

Early in the project, my initial impulse was to process the workflow graph using a simple recursive `while` loop inside a Next.js API route. 

However, LLM calls are inherently flaky—API keys hit rate limits, external endpoints experience latency spikes, and network drops occur. If a recursive loop crashes halfway through a 10-step decision graph, the entire state is lost, forcing expensive and redundant API re-runs.

**My Solution**: I chose **Inngest** as the execution engine. Every graph node is executed as an isolated, durable `step.run()` step. If Node #4 fails due to an API timeout, Inngest automatically retries **only Node #4** without re-executing Nodes #1, #2, or #3. This decision guarantees state integrity and durability at scale.

---

## ⚠️ 1 Real Limitation: Directed Acyclic Graph (DAG) Safety Limits

Currently, the workflow runner is designed for Directed Acyclic Graphs (DAGs). If a user connects Node A → Node B → Node A (creating a cyclical loop), the engine relies on a strict execution limit (maximum 20 steps per run) to prevent infinite API billing loops.

In a future iteration, I plan to introduce explicit loop handle primitives with variable decay conditions so cyclical workflows can be evaluated safely without arbitrary caps.

---

## 📊 Try It & Explore

- **Live Codebase**: `Flyrank/Week-7/ai-workflow-builder`
- **Setup & Eval Results**: Read the full [`README.md`](file:///c:/Users/Abdul%20Hadi/Desktop/Flyrank/Week-7/ai-workflow-builder/README.md)
- **Retrospective**: Read my 500-word [`RETROSPECTIVE.md`](file:///c:/Users/Abdul%20Hadi/Desktop/Flyrank/RETROSPECTIVE.md)

Huge thanks to FlyRank for an intense, practical 8-week journey!
