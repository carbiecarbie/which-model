---
name: which-model
description: Recommends ONE concrete model and ONE reasoning-effort level (e.g. "Opus 5.5 · High" in Claude Code, "GPT-6.1 Sol · Medium" in Codex) for a task or prompt, with a short justification from a scoring rubric and the command to apply it — or, when the user has already picked a model, the right effort for it plus a verdict on whether that model fits. Checks the official model lists on each use so new and retired models are picked up. Use whenever the user asks which model to use, which effort/reasoning level to pick, whether Opus or Sonnet / Sol or Astra is worth it, whether a task needs xhigh/max/ultra, says they want to run a prompt on a specific model ("I want to use Terra on this"), or pastes a prompt asking "what should I run this with?" — even if they don't say "model" (e.g. "does this need a lot of reasoning?", "is this too expensive to run on Opus?").
---

# Which model

The goal is a **definitive** answer: one model, one effort level, one command. No "it depends" and no abstract tier ranges. The person asking wants to paste the command and move on. Uncertainty goes in exactly one place — the "go up / go down if…" lines — not spread across the answer.

This file never names a model. Every model name, ID, price and effort scale lives in `references/models.md`, organized as a **ladder** of rungs and a set of **roles**. That way, when a model launches or retires, only the table changes and every rule here keeps working.

## Step 1 — Check the model lineup

Read `references/models.md`. It has a `Verified on: YYYY-MM-DD` line and the two official source URLs.

**If the table was verified less than 7 days ago** and the user didn't name a model you can't find in it: skip the check and go to Step 2.

**Otherwise, check the two official lists** (fetch both pages; don't use open web search for this step — search results are full of stale and third-party pages):

1. Compare the active models on each page with the table.
2. **Nothing changed** → continue. If you can write to `references/models.md`, update the date.
3. **A model is new** → read its official announcement (and one reputable secondary source if price or effort range is missing) to get: ID, price, effort levels, default/suggested effort, and what the vendor positions it for. Then place it:
   - If it's the successor of a model in the table (same family, newer version — e.g. a new Sonnet after Sonnet 5.5), it takes that model's place in the ladder and role.
   - If the vendor positions it for a niche (vision, computer use, writing, speed) rather than as a general step up or down, list it under **Specialists**.
   - If it's a new top model, it becomes the role "top", which is escalation-only (see adjustment 4).
4. **A model is gone or marked retired** → stop recommending it; use the vendor's named replacement in its rungs.
5. If you could write to the table, record the changes and the date. If you couldn't (the installed skill is read-only, e.g. in the Claude app), use the new data for this answer and add one line at the end: "Model table out of date: <what changed>." — so the user knows to update the skill.
6. If a page can't be fetched, use the table as-is and say in one line that the lineup couldn't be verified today.

Why this matters: models launch and retire every few weeks. Recommending a model that no longer exists — or missing a cheaper one that just launched — is the worst mistake this skill can make. The two-page check is cheap; skipping it on a stale table is not.

## Step 2 — Work out the platform and the mode

**Platform:**
- Running inside Claude Code / Claude → lead with Claude.
- Running inside Codex → lead with Codex.
- If the prompt is clearly written for one harness (it references AGENTS.md conventions, or CLAUDE.md), or the user named a model from one vendor, lead with that one.
- If you can't tell → give both lines.

Always give the main recommendation for the leading platform **and** the equivalent for the other one on a single line, because many people use both. In chosen-model mode, the other-platform line is the ladder recommendation for that platform.

**Mode:**
- The user named a model they want to use ("I want to use Terra on this", "run this on Sonnet?", "is Opus overkill here?") → **chosen-model mode** (Step 5).
- Otherwise → **recommend mode** (Step 4).

## Step 3 — Score the task (0 to 10)

Read the prompt/task and give 0, 1 or 2 on each axis. Score what the task *demands*, not how long the prompt is.

| Axis | 0 | 1 | 2 |
|---|---|---|---|
| **Ambiguity** | Closed spec, obvious result | Some decisions to make | Open goal, has to figure out what to do |
| **Scope** | 1 file / 1 short answer | Several files or steps | Whole project, many stages, long session |
| **Reasoning depth** | Mechanical (rename, format, extract, summarize) | Normal logic, ordinary bug, structured writing | Architecture, hard/concurrency bug, math, cross-domain trade-offs |
| **Cost of error** | Easy to spot and undo | A mistake costs rework | Data loss, security, production, money, irreversible |
| **Verifiability** | Tests/output check everything immediately | Partial checking | Hard to know whether it's right |

Rewriting text with judgment — translation, localization, tone changes — is Reasoning depth 1, not 0: terms must stay consistent and placeholders intact. Only pure format changes (reformatting, renaming, field extraction) are 0.

Add up the five. Keep each axis score — the justification comes from them. Also note the **task kind**: mostly code, mostly prose, mostly images/screenshots/UI, computer use, or bulk mechanical work. The specialist rule needs it.

## Step 4 — Recommend mode: turn the score into a rung

| Score | Rung |
|---|---|
| 0–2 | 1 |
| 3–4 | 2 |
| 5–6 | 3 |
| 7–8 | 4 |
| 9–10 | 5 |

Look up the rung's model and effort for each platform in the **ladder** in `references/models.md`. Then apply these adjustments, in order. They exist because the score alone goes wrong in predictable cases:

1. **Risk floor.** Cost of error = 2 → at least rung 4, even if the total is low. A simple migration command that deletes data is not an "easy task".
2. **Mechanical volume.** The task repeats something well-defined many times (classify 500 items, extract fields, summarize a batch) and Reasoning depth = 0 → rung 1, regardless of scope. A big model here just burns quota.
3. **Mid-tier model doesn't go above High.** Never recommend the *standard*-role model at xhigh or max; recommend the *strong*-role model at Medium instead. A stronger model at medium usually beats a weaker one at its ceiling, for about the same cost.
4. **Top role and Max only as escalation.** Don't recommend the *top*-role model, or Max effort, first. They come in only when the user says rung 5 already failed at this task, or when the score is 10 *and* cost of error is 2. Both eat usage limits very quickly.
5. **Divisible work.** Score ≥ 7 and the task splits into independent parts (e.g. migrate 6 modules, review 4 services) → mention the platform's parallel mode (listed in the table: Ultra in Codex, ultracode in Claude Code). Don't change the model for this. Parts that share one owner, one code path or one test run are not independent.
6. **Price cliffs.** If the context to be sent exceeds a model's price cliff (listed in the table), move to the next model up at its lowest sensible effort — above the cliff the cheap model stops being cheap.
7. **Specialists.** If the task kind matches what a specialist in the table is positioned for (e.g. screenshots, UI review, computer use, prose) and the rung is 4 or lower, use the specialist at the rung's effort instead (not below the specialist's suggested start in the table). Don't use a specialist for complex code or high-risk work — a specialist is not the general step up.

## Step 5 — Chosen-model mode

The user wants a specific model. Respect that: the main line uses their model. Your job is the effort level and an honest one-line verdict.

1. **Resolve the model.** Find it in the table (match loose names: "Terra 6.1" → GPT-6.1 Terra). If it isn't there, Step 1 should already have checked the official lists. If it's retired, say so, name the vendor's replacement, and continue with the replacement.
2. **Score the task** (Step 3) and find the rung recommend mode would give, after adjustments. Call that rung's model the *reference*.
3. **Pick the effort for the chosen model:**
   - Same role as the reference → the rung's effort.
   - Chosen model is one role **stronger** → one effort level lower than the rung's (not below the model's suggested start in the table).
   - Chosen model is one role **weaker** → one effort level higher (cap at High for economy/standard-role models; above that, the verdict is "underpowered").
   - Two or more roles apart → still pick the nearest sensible effort, and let the verdict carry the warning.
   - If the user also fixed the effort, keep their combination on the main line and judge both.
4. **Verdict — one of three, one line, concrete:**
   - **fits** — the chosen model is the reference's role or one step off with a sound effort.
   - **overkill** — the reference is cheaper and enough. Name it and the rough price ratio from the table ("Sol · Medium would do this at ~1/5 of the cost").
   - **underpowered** — the chosen model is weaker than the reference and the task has cost of error 2 or reasoning depth 2. Name the specific risk from the prompt and the model to move to.
   - Specialists have a comparison role in the table. Inside their niche, use that role. Outside it (e.g. a writing/vision model on a hard refactor), count them **one role weaker**, and say why in the verdict.
5. **Go up / Go down stay on the chosen model.** Those lines change only the effort of the user's model, because switching models is what the verdict is for. Only when the chosen model is already at its cap does "Go up" name the next model, starting at Medium.

## Step 6 — Answer format

**Recommend mode** — use exactly this structure, in the user's language:

```
**Recommend: <Model> · <Effort>**
<Other platform>: <Model> · <Effort>

Why (score X/10):
- <highest axis and what in the task caused it>
- <second deciding point, or the adjustment applied>
- <why NOT the level below>

Go up to <model/effort> if: <concrete signal, e.g. "fails the same test twice">
Go down to <model/effort> if: <concrete signal>

`<command to apply>`
```

**Chosen-model mode:**

```
**<Chosen model> · <Effort>** (your pick: <fits | overkill | underpowered>)
<one line: why that verdict, citing the prompt; for overkill/underpowered, name the alternative>
<Other platform>: <Model> · <Effort>

Why this effort (score X/10):
- <deciding axis and what in the task caused it>
- <why not one level lower>

Go up to <effort, or next model at Medium> if: <concrete signal>
Go down to <effort> if: <concrete signal>

`<command to apply>`
```

Format rules:
- The "why" bullets cite something concrete **from the user's prompt**, not generalities ("there's a DB migration with no rollback", not "the task is complex").
- One model and one effort on the main line. If torn between two, the rubric decides; the doubt goes into "Go up / Go down".
- Commands and effort names come from the table (the CLI spelling, e.g. `xhigh`, not "Extra High").
- Don't reproduce the scoring table in the answer; just the total.
- **"Go up to" is always the next cheapest step, never a double jump.** Either the same model one effort level higher, or the next model up starting at **Medium** — never a bigger model *and* high effort at once. If Medium on the bigger model fails, the next request climbs one more step. Double jumps are expensive and are rarely what's needed.
- If Step 1 found the table out of date and couldn't write to it, add the one-line notice at the very end.

## Examples

Model names below are illustrative of the format; always take real ones from the table.

**Recommend mode.** User: "refactor the payment module to use the gateway's new API, it's about 8 files and the integration tests don't cover refunds"

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

**Chosen-model mode.** User: "I want to use Terra on this: add dark-mode screenshots to the docs and check each page's contrast"

```
**GPT-6.1 Terra · Medium** (your pick: fits)
Screenshots and visual checks are exactly what Terra is positioned for, and it costs half of Sol.
Claude: Sonnet 5.5 · Medium

Why this effort (score 4/10):
- Scope is the main load: every docs page needs a capture and a contrast check.
- Low would skip pages whose contrast is borderline; the errors are easy to fix afterwards, so High isn't needed.

Go up to GPT-6.1 Terra · High if: it misses contrast problems you can see.
Go down to GPT-6.1 Terra · Low if: the pages share one theme file and you only need the captures.

`codex -m gpt-6.1-terra -c model_reasoning_effort="medium"`
```
