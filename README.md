# which-model

An agent skill that tells you **which model and which reasoning effort** to run a task with — one concrete answer, not a vague tier.

> **Recommend: Opus 5.5 · High**
> Codex: GPT-6.1 Sol · High
>
> Why (score 7/10): …
>
> Go up to GPT-6 Astra · Medium if: …

It works in **Claude Code** and **Codex** (and any harness that supports the open Agent Skills format).

## How it decides

1. Scores the task 0–2 on five axes: ambiguity, scope, reasoning depth, cost of error, verifiability.
2. Maps the 0–10 total to a model + effort for both Claude and Codex.
3. Applies fixed adjustments: a risk floor, cheap models for mechanical volume, "Opus Medium beats Sonnet xhigh", Fable/Max only as escalation, Ultra/ultracode for divisible work.
4. Escalation is always one step: same model one effort higher, or the bigger model starting at Medium — never both.

**Already picked a model?** Say "I want to use Terra on this" and it keeps your model, picks the effort for it, and gives a one-line verdict: fits, overkill (with the cheaper option) or underpowered (with the specific risk).

**Stays current.** `SKILL.md` never names a model. The ladder, roles and specialists live in `references/models.md`. If that table is more than 7 days old, the skill checks the two official model lists (OpenAI and Anthropic) before answering. New models get slotted into the ladder or listed as specialists, and retired ones are dropped. Where the installed skill is read-only (e.g. the Claude app), it uses the fresh data for that answer and tells you the table needs updating.

## Install

```bash
npx skills add carbiecarbie/which-model
```

Or copy this folder into `~/.claude/skills/which-model/` (Claude Code) or `~/.codex/skills/which-model/` (Codex).

## Use

Paste a prompt and ask "which model should I run this with?" — or just "does this need Opus?".
