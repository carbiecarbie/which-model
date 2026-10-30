# Model table

Verified on: 2026-10-30

This is the only file that names models. Update it — and this date — whenever a check (SKILL.md, Step 1) finds a change.

Official lists to check:
- Codex / OpenAI: https://learn.chatgpt.com/docs/models (developers.openai.com/codex/models redirects here)
- Claude / Anthropic: https://platform.claude.com/docs/en/models/overview (retirements: https://platform.claude.com/docs/en/about-claude/model-deprecations)

## Roles

| Role | Claude | Codex | Meaning |
|---|---|---|---|
| economy | Haiku 5.5 | GPT-6 Luna | Cheapest; clear, repeatable, high-volume work |
| standard | Sonnet 5.5 | GPT-6.1 Sol (Low–Medium) | Everyday work; well-specified tasks |
| strong | Opus 5.5 | GPT-6.1 Sol (High) | Open-ended work, sustained judgment, large refactors |
| frontier | Opus 5.5 (xhigh) | GPT-6 Astra | Hardest tasks that still aren't escalation |
| top (escalation only) | Fable 5.1 | GPT-6 Astra (High, then Max) | Only after rung 5 has failed, or score 10 with cost of error 2 |

In Codex, Sol covers both standard and strong: there is no separate mid model between Luna and Sol, so the role is set by the effort.

## Ladder

| Rung | Claude | Codex |
|---|---|---|
| 1 | Haiku 5.5 · low | GPT-6 Luna · medium |
| 2 | Sonnet 5.5 · medium | GPT-6.1 Sol · low |
| 3 | Opus 5.5 · medium | GPT-6.1 Sol · medium |
| 4 | Opus 5.5 · high | GPT-6.1 Sol · high |
| 5 | Opus 5.5 · xhigh | GPT-6 Astra · medium |
| escalation | Fable 5.1 · medium | GPT-6 Astra · high → max |

Why the Codex rung 5 is Astra at Medium and not xhigh: Astra costs 5× Sol per token, and a bigger model at medium usually beats a smaller one at its ceiling. OpenAI suggests starting Astra at Light.

Why "standard doesn't go above High" (SKILL.md adjustment 3): on CursorBench, Opus 5.5 Medium (52.5%) beats Sonnet 5.5 High, and Sonnet at xhigh costs more than Opus at Medium.

## Specialists

| Model | Platform | Comparison role | Positioned for | Don't use for |
|---|---|---|---|---|
| GPT-6.1 Terra | Codex | standard (half of Sol's price) | Images, screenshots, technical drawings, computer use, clear writing and translation | Complex coding, long agentic runs, high-risk changes (that's Sol) |

Outside its niche, a specialist counts one role weaker than its comparison role (SKILL.md, Step 5).

## Claude models

| Model | ID | API price (in/out per 1M) | Price cliff | Effort levels | Default effort |
|---|---|---|---|---|---|
| Haiku 5.5 | `claude-haiku-5-5` | $0.10 / $0.50 | above 100k-token prompts: $0.50 / $2.50 | low, medium, high, xhigh, max | medium |
| Sonnet 5.5 | `claude-sonnet-5-5` | $2 / $10 | — | low, medium, high, xhigh, max | medium in Claude Code, high on the API |
| Opus 5.5 | `claude-opus-5-5` | $4 / $20 | — | low, medium, high, xhigh, max | medium |
| Fable 5.1 | `claude-fable-5-1` | $10 / $50 | — | low, medium, high, xhigh, max (thinking always on) | high |

- Parallel mode: **ultracode** (a toggle in Claude Code's `/effort` menu; not an effort level).
- Commands: `claude --model <id> --effort <level>`; inside a session `/model` and `/effort <level>`; env var `CLAUDE_CODE_EFFORT_LEVEL`.

## Codex models

| Model | ID | API price (in/out per 1M) | Price cliff | Effort levels | Suggested start |
|---|---|---|---|---|---|
| GPT-6 Luna | `gpt-6-luna` | ~$0.10 / $0.50 (secondary source) | — | low → max (no Ultra) | high |
| GPT-6.1 Terra | `gpt-6.1-terra` | $1 / $5 | above 272k input: $2 / $7.50 | none, low, medium, high, xhigh, max | medium |
| GPT-6.1 Sol | `gpt-6.1-sol` | $2 / $10 | — | low → max, + Ultra | client default |
| GPT-6 Astra | `gpt-6-astra` | $10 / $50 | — | low → max, + Ultra | low (Light) |

- Effort names: CLI/config (`model_reasoning_effort`) uses `low`, `medium`, `high`, `xhigh`; Max and Ultra are under `/model` → "More reasoning…". The app/IDE says Light (= `low`), Medium, High, Extra High (= `xhigh`), Max, Ultra.
- Parallel mode: **Ultra** (splits the task across subagents). Max = more reasoning time on one task. OpenAI says most tasks need neither.
- Effort levels don't map across model generations; don't carry a level over from an older model.
- Commands: `codex -m <id> -c model_reasoning_effort="<level>"`; inside a session `/model`; default in `~/.codex/config.toml`.

## Retired (do not recommend)

- Codex with ChatGPT sign-in: `gpt-5.5` (2026-10-14 → `gpt-6.1-sol`, or `gpt-6-luna` on Free/Go), `gpt-5.3-codex-spark` (2026-09-14), `gpt-5.4` and `gpt-5.4-mini` (2026-08-31), `gpt-5.2`, `gpt-5.3-codex`. Still available on the API with your own key.
- `gpt-5.6-terra` → replaced by `gpt-6.1-terra` (launched 2026-10-26).

## Change log

- 2026-10-30: added GPT-6.1 Terra (specialist); moved the ladder and roles here from SKILL.md.
- 2026-10-18: first version.

## Secondary sources
- https://teamorouter.com/blogs/gpt-6-1-terra-api
- https://apidog.com/blog/claude-sonnet-5-5-claude-code/
- https://paddo.dev/blog/default-was-right/
- https://www.eesel.ai/blog/claude-haiku-5-5-pricing
