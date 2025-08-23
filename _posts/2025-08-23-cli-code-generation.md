---
layout: post
title:  "OpenAI vs Grok vs Claude: CLI Code-Gen Face-off"
date:   2025-08-23 00:00:00 -0400
categories: dev ai
---

About six months ago I went deep on AI chatbots while building one at work to query our internal database. That project turned into a broader realization: these tools make short work of things I’d previously shelved.

Example: finding a par-3 course in Orlando that’s kid-friendly and in budget. DuckDuckGo/Yelp were a slog; an AI chatbot handled it in one prompt. Since then I’ve used chatbots to learn the rules for driving in Italy, build a “soft-spam” IMAP filter, and figure out how to make calls from Paris.

Where I lean on AI the most, is my dev setup. With the AI to lean on when I get stuck, I've had the confidence (and time really) to configure dev tools I could never justify taking the time for previously.

I configured `nvim` with `lazyvim`, added linting with `ruff`, and customized they keybindings for things like `ripgrep`, inserting `import pdb; pdb.set_trace()`, removing trailing whitespace, and so on. Copy/pasting snippets from ChatGPT to shape my Neovim config has been great.

The next step on my AI journey was code generation. I didn’t want to cram more keybindings into my editor, so while I tried the obvious CoPilot, I didn't love it and decided to evaluate **CLI-based** codegen tools instead.

---

## What I Tested

I asked each tool to: **“Write a unit test for the password reset email.”**

Context: Flask app, Peewee ORM, routes under `/auth`, tests with `pytest`. I ran each tool from the repo root and kept tweaks minimal.

---

## OpenAI Codex (CLI)

I started with OpenAI Codex because I like how directly ChatGPT answers. The GitHub web UI was slow but workable for summarizing repos and small edits. On a **non-GitHub** repo with the CLI, I hit some snags.

```bash
npm install -g @openai/codex
export OPENAI_API_KEY=xyz
codex
```

This opens an interactive shell. I asked for the test and it started well, then died on a **token rate-limit**. I tried narrowing scope and switching models; `gpt-4o` and `gpt-5` hit similar limits for me, so I dropped to `gpt-5-mini`:

```bash
codex exec --full-auto -m gpt-5-mini "Write a unit test for the password reset email"
```

Running non-interactive made it easier to iterate configs.

**What it produced:** close, but needed fixes.

* Used `/reset-password` instead of `/auth/reset-password`.
* Imported `User` from `app.models` instead of `app.users.models`.
* Assumed `pytest` fixtures named `app` and `client` (I had to adjust mine).
* Assumed a **clean DB** for tests; my environment reuses a large DB, so I had to adapt.

On the plus side, the tests surfaced a real bug: I was opening a DB connection per request without closing it. SQLite hid it; glad I caught it before moving to Postgres.

---

## Grok (xAI) via CLI

I had high expectations for Grok (lots of buzz post v4). In chat, it can generate a ton of code but sometimes struggles with context switching and adds extra “slop” (more on that below).

There’s no official Grok CLI, so I used a community tool plus Morph for speed:

```bash
npm install -g @vibe-kit/grok-cli
export GROK_API_KEY=xyz
export MORPH_API_KEY=xyz
```

It asks you to add a tiny project profile:

```
.grok/
├── GROK.md          # “Tests are in test/, code is in app/, run with ./run pytest”
└── settings.json    # {"model": "grok-4-latest"}
```

Then:

```bash
grok
# prompt:
# Write a unit test for the password reset email
```

**What it produced:** best pathing of the three.

* Found the right import paths and routes.
* Actually exercised the email-sending flow and monkey-patched user creation, so no DB collisions.
* I still adjusted my fixtures (`app`/`client`).

**Nits:**

* Hard-coded `"test@example.test"` in multiple places; I changed to `"test@example.com"` to satisfy my TLD check.
* Didn’t assert on the email **body**.

It even left helpful comments like:

```py
# To verify body, mock mail.update or inspect msg after update.
```

---

## Claude (Anthropic) — Claude Code CLI

I almost skipped Claude; in chat I’d seen the same “extra slop” as Grok. Then I heard Chamath say his team uses it heavily for codegen, so I tried the CLI—and setup was painless:

```bash
npm install -g @anthropic-ai/claude-code
claude
```

It opens an interactive session and authorizes via the web (no API keys pasted in the shell).

**Prompt:**

```
Write a unit test for the password reset email
```

**What it produced:** three tests out of the gate:

1. Sends password reset for a valid user.
2. Doesn’t send for an invalid user.
3. Validates the form inputs (valid + invalid).

I still updated fixtures, but otherwise it just worked. It also reused variables instead of repeating string literals—nice touch.

---

## About “Slop”

By “slop,” I mean extra code that doesn’t help the task—e.g., asking for a simple login form and getting 30 lines of CSS with shadows and brand colors. For **unit tests**, I don’t mind a bit of over-verbosity; guardrails and fixtures are helpful. For app code, I prefer lean outputs.

Interestingly, none of the tools wrote tests to specifically validate my `mail.send()` function unless I **asked for it**. When I prompted for it explicitly, they did fine. In hindsight, I’m happy they didn’t overreach by default.

---

## Quick Scorecard (my experience)

| Tool              |       Setup | Accuracy on paths/imports | Coverage completeness | Rate limits                               |
| ----------------- | ----------: | ------------------------- | ---------------- | ----------------------------------------- |
| OpenAI Codex CLI  |        Easy | Needed tweaks     | Mid            | **Hit** at higher models; `*-mini` helped |
| Grok CLI (+Morph) |      Medium | Great                 | Mid     | Fine                                      |
| Claude Code CLI   | **Easiest** | Great                      | Excellent  | Fine                                      |

I would note it's probably not fair to compare the open AI mini model against the other premium models but I'm not really tring to compare models here.  I'm just comparing CLI code-gen tools.  If I'm able to get gpt-5 working in this context, I'll certainly post an update.

---

## Takeaways

* **CLI codegen fits my workflow.** I’ve already added plenty to my editor; keeping codegen outside it avoids accidental hotkeys and keeps diffs explicit.
* **Claude Code** was the smoothest overall experience for this task.
* **Grok** understood my repo structure best and handled setup smartly.
* **Codex CLI** is fine, but I ran into rate limits with larger models and had to steer it more.

**Pro Tip:** Give each tool a tiny project profile (like `AGENTS.md` or `.grok/GROK.md`) and run it from repo root. The extra context pays off.

---

## What I’d Do Next

* Add explicit prompts to **assert email body content**.
* Standardize fixtures so “`app`/`client`” works out of the box—most tools assume those names.
* Consider a clean **test DB** (or test schema) even if production data is huge; it avoids a lot of flakiness.
