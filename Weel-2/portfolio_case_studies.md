# Portfolio Case Studies — Abdul Hadi

**Voice card:** Polite, gentle, kind, straightforward, plain, simple.

---

## Case Study 1: Building My First CRUD API

**The problem**

Before this, "API" felt like a huge, complicated word — something with too many fundamentals to get through. I hadn't built one before, and I assumed it would be hard.

**What I did**

I built a Task API in Python using FastAPI, step by step — starting from a basic "hello world" server, then adding one endpoint at a time: listing tasks, getting a single task, creating, updating, and deleting.

The step-by-step order mattered. Once I understood what an endpoint actually was — a path plus a method — the rest stopped feeling complicated. It became a sequence of small, understandable pieces instead of one big scary thing.

One decision I made was around validation: rejecting a task with an empty title. I didn't just follow a rule — I thought about why it matters. If you ask someone for water and they hand you a burger, you're not going to accept it, because it's not what you asked for. Same idea here: if the data doesn't match what's actually being asked for, it shouldn't be allowed through.

**What came of it**

I went from not understanding API fundamentals at all, to genuinely understanding how endpoints work. I can now read an error and diagnose it myself, because I understand the logic behind what I built — not just the code I copied.

---

## Case Study 2: Making My Data Survive — Postgres Migration

**The problem**

My CRUD API worked, but it had one real flaw: every task disappeared the moment the server restarted. Data only lived in memory. The goal this time was to make it survive.

**What I did**

I restructured my code into layers — routes, business logic, and a separate piece just for data storage — so the storage method could be swapped without touching the rest of the code. Then I moved from an in-memory list to a real Postgres database.

Along the way, I hit a wall I didn't expect: my machine's BIOS had virtualization disabled, and the BIOS itself was locked behind a password from a previous admin I couldn't access. Docker couldn't run without it. Honestly, it got me mad — I don't understand why someone would lock a setting like that. But I didn't give up. I might not have completed it the "right" way through Docker, but I stayed dedicated to finishing the actual task, so I installed Postgres directly instead and kept going.

**What came of it**

I proved my data survives — I created a task, restarted the database and the server, and the task was still there. That was the real point of the whole exercise: make data outlive a restart. I did conquer that. The one piece I didn't get to was doing it through Docker specifically, and I'm okay with that — I still reached the actual goal, just by a different road.

---

## Before / After: Generic AI Line vs. My Edited Version

**Generic AI version:**
> "This project demonstrates a results-driven approach to building scalable, robust backend systems, leveraging industry-standard tools to deliver a seamless, production-ready solution."

**My edited version:**
> "I made my task data survive a restart. I hit a wall with Docker I couldn't get around, so I found another way to get there — and it worked."

---

## Bio

I'm Abdul Hadi, a final-year student at COMSATS, in my 7th semester. My interest is backend and AI engineering, and I'm currently doing a backend internship with FlyRank AI, building things step by step and learning as I go. My goal is to become an AI engineer and give my best to this field.

## Contact / Call to Action

If you'd like to talk backend development, AI engineering, or just see more of what I'm building, feel free to reach out.

Email: abdulhadi814981@gmail.com
LinkedIn: Abdul Hadi
