# Capstone Retrospective — Backend AI Engineering Track

**Author**: Abdul Hadi  
**Target Audience**: My Week 1 Self  
**Track**: Backend AI Engineering (FlyRank.ai)  

---

## Dear Week 1 Self,

If you could see what we just built in Week 8, you would hardly recognize the codebase or the developer writing this. 

When you started this track in Week 1, your definition of backend engineering was relatively traditional: writing linear API routes, connecting simple databases, and writing standard error handlers. You thought incorporating AI into a backend meant taking user input, passing it directly to a single LLM API call, and returning whatever raw string came back.

You were about to learn how naive that assumption really was.

---

## What We Set Out to Build vs. What Changed

We set out with a straightforward mission: learn backend engineering and master AI integration. But as the weeks progressed—from creating basic CRUD endpoints, to building a polite web scraper, building background job queues, and implementing LLM endpoints—the core challenge became clear: **LLMs are non-deterministic, while production backends require absolute determinism.**

By Week 7 and Week 8, our ambition evolved into building the **AI Workflow Builder**, a visual graph execution engine powered by Next.js 15, React Flow, and Inngest. 

Instead of treating AI calls as black boxes, we treated each decision node as an isolated, durable function step. When an AI API key rate limits or network connections drop, standard scripts fail and corrupt state. By orchestrating steps with Inngest, our system retries only the failed node, preserving state integrity and stepping through decision trees with strict `YES` or `NO` boolean logic.

---

## What I Would Build Next

If I were to spend another sprint extending the **AI Workflow Builder**, I would focus on three major enhancements:

1. **Stateful Graph Context & Memory**: Currently, each node evaluates its prompt independently. I would implement a context pipeline where upstream decision outputs pass variable payloads down to downstream nodes.
2. **Multi-Model Dynamic Routing**: Allow specific graph nodes to select different models (e.g., lightweight models like `mistral-small` for simple sorting, and reasoning models like `gpt-4o` for complex logic).
3. **Real-time Event-Driven Visualizer**: Replace client-side polling with WebSockets (or Server-Sent Events) so team members can observe multi-tenant workflow executions live simultaneously.

---

## The 3 Most Transferable Things I Learned

### 1. Durable Step-Wise Execution Over Monolithic Scripts
The single biggest breakthrough in how I write software was discovering step-wise durability (using tools like Inngest). Standard `try/catch` blocks in linear scripts fail silently or force total execution restarts when external services drop. Wrapping complex AI orchestrations in isolated steps transforms brittle APIs into production-grade pipelines.

### 2. Schema Enforcement Is Non-Negotiable for AI Backends
You cannot build reliable downstream software logic on unpredictable text blobs. Constraining LLMs through strict output parser schemas (such as enforcing boolean `YES`/`NO` responses or structured JSON output) is the difference between a prototype demo and a reliable backend tool.

### 3. AI as a Strategic Architecture Partner
I moved from asking AI to "write this function for me" to using AI as an architectural co-pilot. Working alongside tools like Claude and Antigravity, my job shifted to system design, edge-case auditing, inspecting log traces, and verifying runtime behavior. AI handles execution speed; the engineer provides rigor and verification.

---

## Final Words to Week 1 Self

You didn't just learn syntax or copy code over these 8 weeks—you built a complete, resilient system from scratch that you can hand to any reviewer, employer, or engineering team with pride. Keep testing, keep building in public, and never accept non-deterministic outputs without a fallback step.

Onward!
