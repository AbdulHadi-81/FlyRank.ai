# Prompt Iteration Log — Code Review/Debug Task

**FL-01 target task:** Help me review/debug backend code for bugs

**Test artifact used for every version** (a real function with real bugs,
based on my project's style):

```python
def update_task(task_id, title=None, done=None):
    conn = get_connection()
    cur = conn.cursor()
    query = "UPDATE tasks SET title = '" + title + "' WHERE id = " + task_id
    cur.execute(query)
    conn.commit()
    return get_task(task_id)
```

---

## Version 0 — Naive baseline

**Prompt:**
> Review this code:
> ```python
> def update_task(task_id, title=None, done=None):
>     conn = get_connection()
>     cur = conn.cursor()
>     query = "UPDATE tasks SET title = '" + title + "' WHERE id = " + task_id
>     cur.execute(query)
>     conn.commit()
>     return get_task(task_id)
> ```

**Output:**
> This function updates a task's title in the database. A few suggestions:
> - Consider adding error handling around the database call
> - You might want to close the cursor/connection when done
> - The `done` parameter isn't currently used in the function
>
> Overall the logic looks reasonable for a basic update function.

**Note:** This is the baseline. It's shallow and dangerously wrong — it
completely misses the SQL injection vulnerability, which is the single most
important bug here. It treats the code as "fine with minor polish" when it's
actually unsafe to run in production.

---

## Version 1 — Role assignment

**Technique:** Role assignment

**Prompt:**
> You are a senior backend security engineer doing a pull request review
> before this code is merged into production. Review this code:
> ```python
> def update_task(task_id, title=None, done=None):
>     conn = get_connection()
>     cur = conn.cursor()
>     query = "UPDATE tasks SET title = '" + title + "' WHERE id = " + task_id
>     cur.execute(query)
>     conn.commit()
>     return get_task(task_id)
> ```

**Output:**
> This code has a critical security issue: it builds the SQL query using
> string concatenation, which makes it vulnerable to SQL injection. An
> attacker could pass a `title` like `'; DROP TABLE tasks; --` and execute
> arbitrary SQL. This needs to use parameterized queries before merging.
> I'd block this PR until it's fixed.

**What changed in the prompt:** Added a specific role ("senior backend
security engineer") and a specific situation (PR review before production).

**What actually improved in the output:** The SQL injection bug — completely
missed in Version 0 — is now the first and central finding. The tone also
shifted from "here are some suggestions" to "I'd block this PR," which
matches how a real reviewer would react to a security bug, not a style nitpick.

**What still failed:** It only caught the security issue. It still didn't
mention the type error (`task_id` is likely an int, but string concatenation
with `+` will crash), the unused `done` parameter, or the missing connection
cleanup.

**What I'd try next:** Give it more context about what this code is actually
part of, so it stops reviewing the snippet in isolation.

---

## Version 2 — Context and motivation

**Technique:** Context and motivation

**Prompt:**
> You are a senior backend security engineer doing a pull request review
> before this code is merged into production. This function is part of a
> FastAPI Task API's repository layer — it's meant to update a task's title
> and/or done status in a Postgres database, matching the same behavior as
> an existing in-memory version it's replacing. Review this code:
> ```python
> def update_task(task_id, title=None, done=None):
>     conn = get_connection()
>     cur = conn.cursor()
>     query = "UPDATE tasks SET title = '" + title + "' WHERE id = " + task_id
>     cur.execute(query)
>     conn.commit()
>     return get_task(task_id)
> ```

**Output:**
> Several issues, in order of severity:
> 1. **SQL injection** — string concatenation in the query lets an attacker
>    inject arbitrary SQL through `title`.
> 2. **Missing `done` handling** — the function accepts a `done` parameter
>    but never updates it, meaning it doesn't actually match the described
>    behavior of updating "title and/or done status."
> 3. **Type error** — `task_id` is likely an integer; concatenating it with
>    `+` into a string query will raise a `TypeError` at runtime, not just a
>    security problem.
> 4. Connection/cursor are never closed, which will eventually exhaust the
>    connection pool under load.

**What changed in the prompt:** Explained what this function is supposed to
do and where it fits (repository layer, replacing an in-memory version).

**What actually improved in the output:** This is the first version to catch
that the function doesn't fulfill its own stated purpose — the `done`
parameter being silently ignored is a functional bug, not just a style issue,
and it only surfaced once the AI knew what "correct" was supposed to look
like. It also now catches the type error, which pure security framing in V1
didn't surface.

**What still failed:** The output is a flat list — no separation between "this
will crash immediately" vs. "this is a slow-burn resource leak." Severity is
implied by order, not stated.

**What I'd try next:** Give it a couple of examples of the kind of review
comments I actually want, so severity is explicit instead of implied.

---

## Version 3 — Few-shot examples

**Technique:** Few-shot examples

**Prompt:**
> You are a senior backend security engineer doing a pull request review
> before this code is merged into production. This function is part of a
> FastAPI Task API's repository layer — it's meant to update a task's title
> and/or done status in a Postgres database, matching the same behavior as
> an existing in-memory version it's replacing.
>
> Here's the style of review comment I want, for reference:
> - "🔴 CRITICAL: Raw string formatting in SQL query allows injection via the
>   `email` field. Fix: use parameterized queries (`%s` placeholders)."
> - "🟡 MINOR: Function doesn't close its DB connection, which will leak
>   connections under sustained load."
>
> Review this code in that same style:
> ```python
> def update_task(task_id, title=None, done=None):
>     conn = get_connection()
>     cur = conn.cursor()
>     query = "UPDATE tasks SET title = '" + title + "' WHERE id = " + task_id
>     cur.execute(query)
>     conn.commit()
>     return get_task(task_id)
> ```

**Output:**
> - 🔴 CRITICAL: Raw string concatenation in the SQL query allows injection
>   via `title`. Fix: use parameterized queries (`%s` placeholders).
> - 🔴 CRITICAL: `task_id` is concatenated directly into the query string;
>   if it's an int (likely, given it's an id), this raises a `TypeError`
>   at runtime, unrelated to injection.
> - 🟡 MINOR: The `done` parameter is accepted but never used — the function
>   silently fails to update it, despite claiming to support it.
> - 🟡 MINOR: Connection and cursor are never closed.

**What changed in the prompt:** Gave two concrete example review comments,
showing the exact severity-tagging style I wanted.

**What actually improved in the output:** Severity is now explicit
(🔴 CRITICAL / 🟡 MINOR) instead of just implied by list order, and it matches
the exact format of the examples — meaning I could paste this straight into
a real PR comment without reformatting it myself. This is the first version
where the output is genuinely ready to use, not just informative.

**What still failed:** It's still a flat list of problems with no fix code —
I know what's wrong, but I'd have to write the corrected version myself.

**What I'd try next:** Ask for a structured output that separates "issues
found" from "corrected code," so both are clearly delivered.

---

## Version 4 — Output structure

**Technique:** Output structure

**Prompt:**
> You are a senior backend security engineer doing a pull request review
> before this code is merged into production. This function is part of a
> FastAPI Task API's repository layer — it's meant to update a task's title
> and/or done status in a Postgres database, matching the same behavior as
> an existing in-memory version it's replacing.
>
> Here's the style of review comment I want, for reference:
> - "🔴 CRITICAL: Raw string formatting in SQL query allows injection via the
>   `email` field. Fix: use parameterized queries (`%s` placeholders)."
> - "🟡 MINOR: Function doesn't close its DB connection, which will leak
>   connections under sustained load."
>
> Structure your answer in exactly two sections:
> 1. "Issues Found" — a tagged list in the style above
> 2. "Corrected Code" — one clean code block with the fixes applied
>
> Review this code:
> ```python
> def update_task(task_id, title=None, done=None):
>     conn = get_connection()
>     cur = conn.cursor()
>     query = "UPDATE tasks SET title = '" + title + "' WHERE id = " + task_id
>     cur.execute(query)
>     conn.commit()
>     return get_task(task_id)
> ```

**Output:**
> **Issues Found**
> - 🔴 CRITICAL: SQL injection via string concatenation in `title`.
> - 🔴 CRITICAL: `task_id` concatenated as string; will crash if it's an int.
> - 🟡 MINOR: `done` parameter accepted but never used.
> - 🟡 MINOR: Connection/cursor never closed.
>
> **Corrected Code**
> ```python
> def update_task(task_id, title=None, done=None):
>     conn = get_connection()
>     try:
>         with conn.cursor() as cur:
>             if title is not None:
>                 cur.execute("UPDATE tasks SET title = %s WHERE id = %s", (title, task_id))
>             if done is not None:
>                 cur.execute("UPDATE tasks SET done = %s WHERE id = %s", (done, task_id))
>             conn.commit()
>     finally:
>         conn.close()
>     return get_task(task_id)
> ```

**What changed in the prompt:** Explicitly named two required output
sections and their order.

**What actually improved in the output:** Now I get both the diagnosis *and*
a ready-to-use fix in one clearly separated response, rather than having to
ask a follow-up question to get corrected code. This is meaningfully more
useful to actually act on, not just read.

**What still failed:** The AI reviewed and fixed everything in one pass — for
a genuinely tricky bug, I'd rather see its reasoning about *why* each fix
works before jumping to the final code, especially the injection fix.

**What I'd try next:** Break the task into explicit steps, so reasoning
happens before the fix, not folded invisibly into it.

---

## Version 5 — Step decomposition

**Technique:** Step decomposition

**Prompt:**
> You are a senior backend security engineer doing a pull request review
> before this code is merged into production. This function is part of a
> FastAPI Task API's repository layer — it's meant to update a task's title
> and/or done status in a Postgres database, matching the same behavior as
> an existing in-memory version it's replacing.
>
> Here's the style of review comment I want, for reference:
> - "🔴 CRITICAL: Raw string formatting in SQL query allows injection via the
>   `email` field. Fix: use parameterized queries (`%s` placeholders)."
> - "🟡 MINOR: Function doesn't close its DB connection, which will leak
>   connections under sustained load."
>
> Work through this in three explicit steps:
> 1. List every issue found, tagged by severity, in the style above.
> 2. For each 🔴 CRITICAL issue only, explain *why* the fix works, not just
>    what it is.
> 3. Then give one clean corrected code block with all fixes applied.
>
> Review this code:
> ```python
> def update_task(task_id, title=None, done=None):
>     conn = get_connection()
>     cur = conn.cursor()
>     query = "UPDATE tasks SET title = '" + title + "' WHERE id = " + task_id
>     cur.execute(query)
>     conn.commit()
>     return get_task(task_id)
> ```

**Output:**
> **Step 1 — Issues Found**
> - 🔴 CRITICAL: SQL injection via string concatenation in `title`.
> - 🔴 CRITICAL: `task_id` concatenated as string; will crash if it's an int.
> - 🟡 MINOR: `done` parameter accepted but never used.
> - 🟡 MINOR: Connection/cursor never closed.
>
> **Step 2 — Why the critical fixes work**
> - Parameterized queries (`%s` placeholders) work because the database
>   driver sends the value separately from the SQL command itself — the
>   database always treats it as *data*, never as part of the executable
>   query, so injected SQL syntax inside `title` can't be interpreted as
>   commands.
> - Using `%s` with the actual `task_id` value (rather than string-concatenating
>   it) also sidesteps the type error, since the driver handles type
>   conversion instead of Python's `+` operator.
>
> **Step 3 — Corrected Code**
> ```python
> def update_task(task_id, title=None, done=None):
>     conn = get_connection()
>     try:
>         with conn.cursor() as cur:
>             if title is not None:
>                 cur.execute("UPDATE tasks SET title = %s WHERE id = %s", (title, task_id))
>             if done is not None:
>                 cur.execute("UPDATE tasks SET done = %s WHERE id = %s", (done, task_id))
>             conn.commit()
>     finally:
>         conn.close()
>     return get_task(task_id)
> ```

**What changed in the prompt:** Explicitly ordered the task into three
steps, and required reasoning specifically for critical issues before the fix.

**What actually improved in the output:** I now understand *why* the fix
works (parameterized queries separate data from command structure), not just
that it's "the correct pattern to use." That's the difference between being
able to explain this fix to a teammate versus just copy-pasting it without
understanding it.

**What still failed / honest limitation:** This step decomposition didn't
change the final code output at all compared to Version 4 — the corrected
code block is identical. The value here is purely in my own understanding,
not in the deliverable code itself. If the goal were only "get working
code fast," this extra step is arguably wasted effort — a genuine case
where more structure added value for learning but not for the artifact.

**What I'd try next:** This is close to a final template — the main
remaining question is whether this holds up on a completely different bug,
and how it performs on a different model entirely (next section).

---

## Cross-Model Comparison

**Final prompt used on both models** (Version 5's prompt, with the code
review request generalized so it's reusable — see Final Template below).

**Claude's output:** As shown in Version 5 above — tagged severity list,
then a "why it works" explanation for critical issues only, then one
corrected code block.

**ChatGPT's output (actual, pasted by user):**
> The function itself does not print anything. It updates a task in the
> database and returns the updated task. For example, if you call:
> `update_task("1", "Complete assignment")`
> The SQL query created will be:
> `UPDATE tasks SET title = 'Complete assignment' WHERE id = 1`
> ...
> **Note:** This code is unsafe because it builds the SQL query using string
> concatenation... It is vulnerable to SQL Injection. The safer way is to
> use parameterized queries:
> `query = "UPDATE tasks SET title = %s WHERE id = %s"`
> `cur.execute(query, (title, task_id))`

**My honest comparison:**

- **Format compliance:** ChatGPT ignored the requested structure almost
  entirely. I asked for three explicit steps (tagged issue list → "why it
  works" reasoning for critical issues → one corrected code block). Instead
  it gave a walkthrough of what the code does when called, with an example
  execution trace I never asked for, and folded the one issue it found into
  a single "Note" at the end. Claude followed the three-step structure exactly.

- **Issue coverage — this is the biggest difference:** ChatGPT caught
  *only* the SQL injection bug. It completely missed:
  - The type error (`task_id` is concatenated as a string; if it's an int,
    this crashes at runtime) — in fact, ChatGPT's own example
    (`update_task("1", ...)`) quietly passes `task_id` as a *string*, which
    sidesteps the very bug it should have caught, rather than testing with
    an int like a real caller would use.
  - The `done` parameter being accepted but silently never used — a real
    functional bug, not just a security one.
  - The connection/cursor never being closed.

  Claude's Version 5 caught all four issues, tagged by severity. ChatGPT
  caught 1 of 4, with no severity tagging at all.

- **Tone:** Claude's output read like an actual PR review comment ("I'd
  block this PR," 🔴/🟡 tags). ChatGPT's read like a tutorial explanation of
  what the code does — helpful for understanding, but not shaped like a
  code review at all. The "senior security engineer" role I assigned didn't
  visibly change ChatGPT's voice the way it changed Claude's.

- **Why it works" reasoning:** ChatGPT did explain parameterized queries
  briefly, but didn't connect it back to *why* it also fixes the type
  error — it treated the fix as purely a security patch, missing that it
  also happens to solve the crash bug for free.

**Takeaway:** The same engineered prompt did not transfer equally well
across models. Claude followed the explicit structure and caught more real
bugs; ChatGPT reverted to an explanatory, tutorial-style answer and missed
75% of the actual issues in the code. This suggests the prompt's structure
instructions need to be even more explicit/repetitive to hold up on models
that don't follow structure requests as tightly.

---

## Final Reusable Template

> You are a senior [YOUR DOMAIN] engineer doing a pull request review before
> this code is merged into production.
>
> Context: this code is part of [describe the system/layer it belongs to,
> and what it's supposed to do].
>
> Here's the style of review comment I want, for reference:
> - "🔴 CRITICAL: [example of a serious issue] Fix: [example of the fix]."
> - "🟡 MINOR: [example of a small issue]."
>
> Work through this in three steps:
> 1. List every issue found, tagged by severity, in the style above.
> 2. For each 🔴 CRITICAL issue only, explain *why* the fix works, not just
>    what it is.
> 3. Then give one clean corrected code block with all fixes applied.
>
> Review this code:
> ```
> [PASTE CODE HERE]
> ```

**Why this works for a stranger:** every bracket is a drop-in point for
someone else's actual domain, system, and code — nothing depends on knowing
my Task API. The five techniques (role, context, few-shot, structure, step
decomposition) are what make the *shape* of a good review happen reliably,
regardless of whose code goes in the brackets.
