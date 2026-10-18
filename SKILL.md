---
name: which-model
description: Recommends ONE concrete model and ONE reasoning-effort level (e.g. "Opus 5.5 · High" in Claude Code, "GPT-6.1 Sol · Medium" in Codex) for a task or prompt, with a short justification from a scoring rubric and the command to apply it. Use whenever the user asks which model to use, which effort/reasoning level to pick, whether Opus or Sonnet / Sol or Astra is worth it, whether a task needs xhigh/max/ultra, or pastes a prompt asking "what should I run this with?" — even if they don't say "model" (e.g. "does this need a lot of reasoning?", "is this too expensive to run on Opus?").
---

# Which model

The goal is a **definitive** answer: one model, one effort level, one command. No "it depends" and no abstract tier ranges. The person asking wants to paste the command and move on. Uncertainty goes in exactly one place — the "go up / go down if…" lines — not spread across the answer.

## Step 1 — Make sure the model table is current

Read `references/models.md`. It has a `Verified on: YYYY-MM-DD` line.

- If that date is **more than 30 days old**, or the user names a model that isn't in the table, or there is any sign something changed (a retirement, a launch): search the web before recommending. Primary sources: the Codex models page (`developers.openai.com/codex/models`), the Claude Code effort documentation (`code.claude.com/docs`), and the Anthropic and OpenAI model announcements. Update the table with what you find (including the date) if you can write to it; if not, use the new data for this answer only and say in one line that the skill's table is out of date.
- If it is within 30 days, use the table without searching — unnecessary searching only adds delay.

Why this matters: models are retired often (Codex retired gpt-5.4, Spark and gpt-5.5 within a few months). Recommending a model that no longer exists is the worst mistake this skill can make.

## Step 2 — Work out the platform

- Running inside Claude Code / Claude → recommend for Claude first.
- Running inside Codex → recommend for Codex first.
- If the prompt itself is clearly written for one harness (e.g. it references AGENTS.md conventions, or CLAUDE.md), lead with that one.
- If the user asked for both, or you can't tell → give both lines.

Always give the main recommendation for the leading platform **and** the equivalent for the other one on a single line, because many people use both.

## Step 3 — Score the task (0 to 10)

Read the prompt/task and give 0, 1 or 2 on each axis. Score what the task *demands*, not how long the prompt is.

| Axis | 0 | 1 | 2 |
|---|---|---|---|
| **Ambiguity** | Closed spec, obvious result | Some decisions to make | Open goal, has to figure out what to do |
| **Scope** | 1 file / 1 short answer | Several files or steps | Whole project, many stages, long session |
| **Reasoning depth** | Mechanical (rename, format, extract, summarize) | Normal logic, ordinary bug, structured writing | Architecture, hard/concurrency bug, math, cross-domain trade-offs |
| **Cost of error** | Easy to spot and undo | A mistake costs rework | Data loss, security, production, money, irreversible |
| **Verifiability** | Tests/output check everything immediately | Partial checking | Hard to know whether it's right |

Add up the five. Keep each axis score — the justification comes from them.

## Step 4 — Turn the score into a recommendation

| Score | Claude | Codex |
|---|---|---|
| 0–2 | **Haiku 5.5 · Low** | **GPT-6 Luna · Medium** |
| 3–4 | **Sonnet 5.5 · Medium** | **GPT-6.1 Sol · Low** |
| 5–6 | **Opus 5.5 · Medium** | **GPT-6.1 Sol · Medium** |
| 7–8 | **Opus 5.5 · High** | **GPT-6.1 Sol · High** |
| 9–10 | **Opus 5.5 · xhigh** | **GPT-6 Astra · Medium** |

Why Astra comes in at Medium rather than xhigh: Astra costs 5× Sol per token. A bigger model at medium effort usually outperforms the smaller one at its ceiling, and costs far less than the bigger one at its ceiling. OpenAI itself suggests starting Astra at Light.

Then apply these adjustments, in this order. They exist because the score alone goes wrong in predictable cases:

1. **Risk floor.** Cost of error = 2 → at least Opus 5.5 · High / GPT-6.1 Sol · High, even if the total is low. A simple migration command that deletes data is not an "easy task".
2. **Mechanical volume.** If the task repeats something well-defined many times (classify 500 items, extract fields, summarize a batch) and Reasoning depth = 0 → Haiku 5.5 / GPT-6 Luna, regardless of scope. A big model here just burns quota.
3. **Sonnet doesn't go above High.** If you were about to recommend Sonnet 5.5 at xhigh/max, recommend Opus 5.5 · Medium instead: in public benchmarks Opus at Medium beats Sonnet at High and costs about the same as Sonnet at xhigh.
4. **Fable 5.1 / Astra Max only as escalation.** Don't recommend Fable 5.1 or Max first. They come in only when the user says Opus 5.5 xhigh (or Astra High) already failed at this task, or when the score is 10 *and* cost of error is 2. Both eat usage limits very quickly.
5. **Divisible work.** Score ≥ 7 and the task splits into independent parts (e.g. migrate 6 modules, review 4 services) → add: Codex with **Ultra** (subagents) or Claude Code with **ultracode** on. Don't change the model for this, just mention it. Parts that share one owner, one code path or one test run are not independent.
6. **Huge context on Haiku.** If more than 100k tokens of context will be sent, swap Haiku 5.5 for Sonnet 5.5 · Low: above 100k Haiku 5.5's price rises 5× and its advantage disappears.

## Step 5 — Answer in this format

Use exactly this structure, in the user's language:

```
**Recommend: <Model> · <Effort>**
Codex: <Model> · <Effort>   (or "Claude: ..." if leading with Codex)

Why (score X/10):
- <highest axis and what in the task caused it>
- <second deciding point, or the adjustment applied>
- <why NOT the level below>

Go up to <model/effort> if: <concrete signal, e.g. "fails the same test twice">
Go down to <model/effort> if: <concrete signal>

`<command to apply>`
```

Format rules:
- The three "why" bullets cite something concrete **from the user's prompt**, not generalities ("there's a DB migration with no rollback", not "the task is complex").
- One model and one effort on the main line. If torn between two, the rubric decides; the doubt goes into "Go up / Go down".
- Commands: Claude Code → `claude --model claude-opus-5-5 --effort high` (or `/model` + `/effort high` inside the session). Codex → `codex -m gpt-6.1-sol -c model_reasoning_effort="high"` (or `/model` inside the session). Take IDs and effort names from the reference table.
- Don't reproduce the scoring table in the answer; just the total.
- **"Go up to" is always the next cheapest step, never a double jump.** Either the same model one effort level higher, or the bigger model starting at **Medium** — never a bigger model *and* high effort at once. E.g. from GPT-6.1 Sol · High, go up to GPT-6 Astra · Medium (not Astra · Extra High); from Opus 5.5 · xhigh, go up to Fable 5.1 · Medium. If Medium on the bigger model fails, the next request climbs one more step. Double jumps are expensive and are rarely what's needed.

## Example

User prompt: "refactor the payment module to use the gateway's new API, it's about 8 files and the integration tests don't cover refunds"

```
**Recommend: Opus 5.5 · High**
Codex: GPT-6.1 Sol · High

Why (score 7/10):
- High cost of error: it's the payment flow and refunds have no tests — a bug would slip through unnoticed.
- 8 files against a new API means keeping many decisions consistent with each other.
- Medium tends to follow the obvious path without checking refunds; here the extra care pays off.

Go up to Opus 5.5 · xhigh if: it gets the refund logic wrong twice.
Go down to Opus 5.5 · Medium if: you write the refund tests first (verifiability goes up).

`claude --model claude-opus-5-5 --effort high`
```
