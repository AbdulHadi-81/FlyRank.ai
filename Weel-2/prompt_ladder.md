# Prompt Ladder — Backend Track

**Track:** Backend development (FlyRank Internship)

---

## Baseline (Version 0)

**Prompt:**
> Write backend code

**Output:**
```javascript
const express = require('express');
const app = express();
app.get('/', (req, res) => res.send('Hello World'));
app.listen(3000, () => console.log('Server running'));
```
> This creates a basic server. Let me know if you'd like to add more routes,
> database integration, or authentication!

**Notes:** N/A — this is the baseline. It's a generic hello-world with zero
connection to any real task. Could apply to literally anyone, in any language,
for any purpose.

---

## Version 1 — Add a clearer goal

**Layer added:** Clearer goal

**Prompt:**
> Write backend code for an API endpoint that lets a user reset their password.

**Output (excerpt):**
```javascript
app.post('/reset-password', async (req, res) => {
  const { email } = req.body;
  const user = await User.findOne({ email });
  if (!user) return res.status(404).send('User not found');
  const resetToken = generateToken();
  user.resetToken = resetToken;
  await user.save();
  sendEmail(user.email, resetToken);
  res.send('Reset email sent');
});
```

**What changed in the prompt:** Added a specific goal — password reset — instead of "backend code" with no target.

**What actually improved in the output:** The output stopped being a generic hello-world and became a real, relevant route with actual logic (finding a user, generating a token, sending an email). It's now aimed at something.

**What still failed:** No language/framework/database was specified beyond guessing Express + some ORM. It also leaks whether an email exists in the system (`404 User not found`), a real security problem the AI didn't flag on its own.

**What I'd try next:** Tell the AI who this code is for, so it stops guessing at the stack.

---

## Version 2 — Add a defined audience

**Layer added:** Defined audience

**Prompt:**
> Write backend code for an API endpoint that lets a user reset their password.
> The audience is a junior backend developer on my team who will review and
> merge this code, so explain the security reasoning behind each step.

**Output (excerpt):**
```javascript
app.post('/reset-password', async (req, res) => {
  const { email } = req.body;
  const user = await User.findOne({ email });

  // Always respond the same way whether or not the user exists,
  // so attackers can't use this endpoint to check which emails are registered.
  if (user) {
    const resetToken = generateToken();
    user.resetToken = resetToken;
    await user.save();
    sendEmail(user.email, resetToken);
  }

  res.send('If that email exists, a reset link has been sent.');
});
```
> Explanation for review: this avoids leaking account existence (a common
> security mistake in reset-password flows)...

**What changed in the prompt:** Told the AI who reads this code and why (a junior teammate reviewing it), and asked for reasoning, not just code.

**What actually improved in the output:** It actually fixed the security leak from Version 1 on its own, and added a comment explaining why — something a real teammate reviewing this would need to know, not just what the code does.

**What still failed:** Still hasn't been told what stack/framework I'm actually using — it's guessing Express + Mongoose again.

**What I'd try next:** Give it real, specific context about the actual project it needs to fit into.

---

## Version 3 — Add real context

**Layer added:** Real context

**Prompt:**
> Write backend code for an API endpoint that lets a user reset their password.
> The audience is a junior backend developer on my team who will review and
> merge this code, so explain the security reasoning behind each step.
> Context: this is for my Task API project, built in Python with FastAPI,
> using a layered architecture (routes / service / repository), with Postgres
> as the database via psycopg2. Match this existing structure — don't
> introduce a different framework or pattern.

**Output (excerpt):**
```python
# routes (main.py)
@app.post("/reset-password")
def reset_password(request: ResetRequest):
    service.request_password_reset(request.email)
    return {"message": "If that email exists, a reset link has been sent."}

# service.py
def request_password_reset(email: str):
    user = repository.get_user_by_email(email)
    if user:
        token = generate_reset_token()
        repository.save_reset_token(user["id"], token)
        send_reset_email(email, token)
    # No error raised either way — same response regardless, to avoid
    # leaking which emails are registered.

# repository.py
def get_user_by_email(email: str):
    conn = _get_connection()
    ...
```

**What changed in the prompt:** Gave the actual language, framework, architecture pattern, and database already in use.

**What actually improved in the output:** This is the biggest jump so far — the code now actually fits my real project. It split across routes/service/repository exactly like my existing files, instead of inventing an unrelated Express/Mongoose stack I'd have to throw away and rewrite from scratch.

**What still failed:** The output structure (three separate snippets) was a little awkward to read as one flowing answer — no clear labeling of which code goes in which file until I looked closely.

**What I'd try next:** Ask for a specific output format so it's unambiguous which code belongs where.

---

## Version 4 — Specify output format

**Layer added:** Specified output format

**Prompt:**
> Write backend code for an API endpoint that lets a user reset their password.
> The audience is a junior backend developer on my team who will review and
> merge this code, so explain the security reasoning behind each step.
> Context: this is for my Task API project, built in Python with FastAPI,
> using a layered architecture (routes / service / repository), with Postgres
> as the database via psycopg2. Match this existing structure.
> Format the answer as three clearly labeled sections — "main.py", "service.py",
> "repository.py" — each in its own code block, in that order, with a one-line
> comment above each explaining that file's job.

**Output (excerpt):**
> **main.py**
> ```python
> # Route only — receives the request, delegates to the service layer
> @app.post("/reset-password")
> def reset_password(request: ResetRequest):
>     service.request_password_reset(request.email)
>     return {"message": "If that email exists, a reset link has been sent."}
> ```
> **service.py**
> ```python
> # Business logic — decides what "reset password" actually means
> def request_password_reset(email: str):
>     ...
> ```
> **repository.py**
> ```python
> # Data access only — the one file that talks to Postgres
> def get_user_by_email(email: str):
>     ...
> ```

**What changed in the prompt:** Asked for explicit labeled sections in a fixed order, matching my file structure.

**What actually improved in the output:** Now it's genuinely copy-pasteable straight into my three real files with zero guessing about placement — before, I had to reread and figure out which snippet went where.

**What still failed:** Nothing broke, but the output still assumes things about my exact table schema (column names) that it's just guessing at.

**What I'd try next:** Add real constraints — the actual schema and rules that exist in my project.

---

## Version 5 — Add constraints

**Layer added:** Constraints

**Prompt:**
> Write backend code for an API endpoint that lets a user reset their password.
> The audience is a junior backend developer on my team who will review and
> merge this code, so explain the security reasoning behind each step.
> Context: this is for my Task API project, built in Python with FastAPI,
> using a layered architecture (routes / service / repository), with Postgres
> as the database via psycopg2. Match this existing structure.
> Format the answer as three clearly labeled sections — "main.py", "service.py",
> "repository.py" — each in its own code block, in that order, with a one-line
> comment above each explaining that file's job.
> Constraints: my `tasks` table has no `users` table yet — don't assume one
> exists, just show where user lookup would plug in with a TODO comment.
> Keep each code block under 15 lines. Don't add any new pip packages.

**Output (excerpt):**
> **main.py**
> ```python
> # Route only — receives the request, delegates to the service layer
> @app.post("/reset-password")
> def reset_password(request: ResetRequest):
>     service.request_password_reset(request.email)
>     return {"message": "If that email exists, a reset link has been sent."}
> ```
> **service.py**
> ```python
> # Business logic
> def request_password_reset(email: str):
>     # TODO: replace with real user lookup once a users table exists
>     user = repository.get_user_by_email(email)
>     if user:
>         ...
> ```
> **repository.py**
> ```python
> # TODO: no users table yet — stub only
> def get_user_by_email(email: str):
>     pass
> ```

**What changed in the prompt:** Added the honest constraint that no `users` table exists yet, plus a line-count limit and "no new packages."

**What actually improved in the output:** It correctly stopped inventing a fake `users` table and schema it had been quietly guessing at since Version 3 — that guessing is gone now, replaced with an honest TODO.

**What still failed / made it worse:** The 15-line limit actually hurt this version — to stay under it, the AI stripped out the token-generation and email-sending logic entirely, leaving basically a skeleton with TODOs instead of real, reviewable code. Version 4's output was more useful to actually hand to a teammate than this one, even though this one is "more accurate" about my schema. This is the one layer that made things worse in a real sense — an over-constrained prompt produced a less useful answer.

**What I'd try next:** Drop the line-count limit, but keep the "don't assume a users table" honesty constraint — that part was genuinely valuable, the length limit wasn't.

---

## Final Reusable Prompt

*(Combining what actually helped — Versions 1–4 in full, keeping only the useful half of Version 5's constraint, dropping the harmful line limit)*

> Write backend code for [describe the specific feature/endpoint].
>
> **Audience:** [who reviews/uses this code, e.g. "a junior developer on my
> team who will review and merge this"] — explain the reasoning behind
> non-obvious decisions (especially security ones), not just what the code does.
>
> **Context:** This is for [your project name], built in [language/framework],
> using [your actual architecture pattern], with [your actual database/tools].
> Match this existing structure — don't introduce a different framework or
> pattern than what's already in use.
>
> **Format:** Structure the answer as clearly labeled sections matching my
> actual file names — [list your real files, e.g. "main.py", "service.py",
> "repository.py"] — each in its own code block, in that order, with a
> one-line comment at the top of each explaining that file's job.
>
> **Honesty constraint:** If something you'd need (like a table, a config
> value, or another function) doesn't exist yet in my project, don't invent
> it — mark it clearly with a TODO comment instead of assuming it's there.

**Why this version works for a stranger, not just me:** every bracket is a
place to drop in real specifics about *their* project — nothing here depends
on knowing my Task API specifically. The structure (goal → audience → context
→ format → honesty constraint) is the reusable skeleton; the brackets are
where their own details go.
