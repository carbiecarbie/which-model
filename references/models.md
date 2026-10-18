# Model table

Verified on: 2026-10-18

Update this date whenever you review the table. If it is more than 30 days old, search before using it (see SKILL.md, Step 1).

## Claude (Claude Code / Claude.ai)

| Model | ID | API price (in/out per 1M) | Effort levels | Default in Claude Code | Role |
|---|---|---|---|---|---|
| Haiku 5.5 | `claude-haiku-5-5` | $0.10 / $0.50 up to 100k tokens; $0.50 / $2.50 above | low, medium, high, xhigh, max | medium | Fast and cheap: summarizing, extraction, classification, mechanical work at volume |
| Sonnet 5.5 | `claude-sonnet-5-5` | $2 / $10 | low, medium, high, xhigh, max | medium (API: high) | Well-specified tasks, bug fixes, everyday code |
| Opus 5.5 | `claude-opus-5-5` | $4 / $20 | low, medium, high, xhigh, max | medium (Claude Code default since v2.1.280) | Open-ended work, sustained judgment, large refactors |
| Fable 5.1 | `claude-fable-5-1` | $10 / $50 | low, medium, high, xhigh, max (thinking always on) | high | Escalation only: when Opus 5.5 at xhigh/max can't do it |

Notes:
- Per Anthropic, Opus 5.5 at Medium matches or beats Opus 5 at High. In an independent test on real monorepo changes, Opus 5.5 did best at Medium.
- CursorBench: Opus 5.5 Medium (52.5%) > Sonnet 5.5 High; Sonnet at xhigh costs more than Opus at Medium. Hence the rule "if you'd use Sonnet at xhigh, use Opus at Medium".
- Sonnet 5.5 vs Opus 5.5 on SWE-Bench Pro (max): 81.3 vs 89.9. Long context (ProgramBench): 79.7 vs 91.2.
- Ultracode: an option in Claude Code's `/effort` menu that orchestrates subagents; it is not an effort level.
- Commands: `claude --model <id> --effort <level>`; inside a session `/model` and `/effort <level>`; env var `CLAUDE_CODE_EFFORT_LEVEL`.

## Codex (OpenAI — CLI, IDE, app)

| Model | ID | API price (in/out per 1M) | Effort levels | OpenAI's suggested start | Role |
|---|---|---|---|---|---|
| GPT-6 Luna | `gpt-6-luna` | cheapest in the lineup | low → max (no Ultra) | High | Clear, repeatable tasks: extraction, classification, transformation, structured summaries |
| GPT-6.1 Sol | `gpt-6.1-sol` | $2 / $10 | low → max, + Ultra | client default | Default for complex code and agentic work; near-Astra quality |
| GPT-6 Astra | `gpt-6-astra` | $10 / $50 | low → max, + Ultra | Light (`low`) | Most capable: the hardest end-to-end work |

Effort names:
- CLI/config (`model_reasoning_effort`): `low`, `medium`, `high`, `xhigh`; Max and Ultra appear under `/model` → "More reasoning…".
- App/IDE: Light, Medium, High, Extra High, Max, Ultra. Light = `low`, Extra High = `xhigh`.
- Max = more reasoning time on a single task. Ultra = splits the task across parallel subagents. OpenAI says most tasks need neither.
- Effort levels don't map across model generations; don't carry a level over from an older model.

Retired from Codex with ChatGPT sign-in (do not recommend): `gpt-5.5` (2026-10-14), `gpt-5.3-codex-spark` (2026-09-14), `gpt-5.4` and `gpt-5.4-mini` (2026-08-31), `gpt-5.2`, `gpt-5.3-codex`. They remain on the API with your own key.

Commands: `codex -m <id> -c model_reasoning_effort="<level>"`; inside a session `/model`; default in `~/.codex/config.toml` (`model = "..."`, `model_reasoning_effort = "..."`). TUI shortcuts: Alt+, lowers and Alt+. raises effort.

## Sources
- https://developers.openai.com/codex/models
- https://www.anthropic.com/claude-sonnet-5-5
- https://openclawdatabase.com/claude-cowork/faq/effort-levels/
- https://apidog.com/blog/claude-sonnet-5-5-claude-code/
- https://paddo.dev/blog/default-was-right/
- https://www.eesel.ai/blog/claude-haiku-5-5-pricing
- https://aidenapp.org/best-codex-model
