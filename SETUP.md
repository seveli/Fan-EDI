# WealthCompass — Setup Guide

One-time steps to get this project running with Claude Code.

## Prerequisites

- **Claude Code** installed and authenticated. See https://docs.claude.com/claude-code for current install instructions.
- **Python 3.11+** (`python3 --version`).
- **Node.js 18+** (required by Claude Code itself).
- **Git** for version control.
- *(Optional, Phase E only)* An Anthropic API key for the AI advisor.

## 1. Create the project directory

```bash
mkdir wealthcompass
cd wealthcompass
mkdir docs
git init
```

## 2. Place the four planning documents

You should have downloaded these from the prior conversation. Place them like this:

```
wealthcompass/
├── CLAUDE.md                              ← project root
├── kickoff_prompt.md                      ← project root (reference for you)
├── SETUP.md                               ← this file (project root)
└── docs/
    ├── WealthCompass_PRD.md
    └── WealthCompass_Build_Plan.md
```

Verify with:

```bash
ls -la
ls -la docs/
```

## 3. Initial commit

```bash
git add -A
git commit -m "chore: project planning documents"
```

This is your “before any code” baseline. Easy to diff against later.

## 4. (Optional) Set the Anthropic API key

Required only for Phase E (Tasks 28–30, the AI advisor). Phases A–D run entirely offline.

```bash
# bash/zsh
export ANTHROPIC_API_KEY='sk-ant-...'

# or add to a .env file (gitignored — Claude Code will create .env.example in Task 1)
echo "ANTHROPIC_API_KEY=sk-ant-..." > .env
```

## 5. Start a Claude Code session

```bash
claude
```

Then open `kickoff_prompt.md` in another window, copy the content between the `---` lines, and paste it as your first message.

## Working rhythm

After each task Claude Code completes:

1. **Review the diff** — `git diff` (or use the diff Claude Code shows you).
1. **Run the tests yourself** if you want belt-and-suspenders — `make test`.
1. **Inspect any new files** to make sure they match the Build Plan’s spec.
1. **Commit** — Claude Code may do this for you; if not, `git add -A && git commit -m "<message> (Task N)"`.
1. **Reply “next”** to continue.

If a task’s output looks wrong:

- Quote the specific PRD section or Build Plan task line that the implementation violates.
- Ask Claude Code to redo the task with that constraint highlighted.

## When to use `/clear`

Claude Code’s context window is finite. Around Task 15–20, sessions can get long enough that you’ll see degraded responses. When that happens:

1. Make sure the last task is committed.
1. Use `/clear` inside the Claude Code session.
1. Re-paste the kickoff prompt. Add: “We just finished Task N. Begin with Task N+1.”

The docs in the repo are the persistent memory; the in-session context is short-term.

## Phase milestones — sanity checks

|After Phase    |You should be able to                                            |Time estimate|
|---------------|-----------------------------------------------------------------|-------------|
|A (Tasks 1–6)  |Run `streamlit run app.py` and see a landing page with disclaimer|1–2 days     |
|B (Tasks 7–14) |Run `make test` and see all domain math passing                  |3–5 days     |
|C (Tasks 15–18)|Run portfolio analysis on seeded holdings via a Python REPL      |1–2 days     |
|D (Tasks 19–27)|Use the full app interactively with manual data                  |3–5 days     |
|E (Tasks 28–30)|Ask the AI advisor a question and get a structured response      |1–2 days     |
|F (Tasks 31–34)|Download a PDF and Excel report                                  |1 day        |
|G (Tasks 35–37)|CI passes on push; coverage ≥90% on `domain/`                    |1 day        |

Total: ~3 weeks of focused work, longer if you’re squeezing it into evenings.

## Troubleshooting

**Claude Code keeps trying to start with the UI.**
Reply: “Stop. Per the Build Plan, UI is Phase D (Task 19+). We’re on Task N. Re-read CLAUDE.md and the Build Plan and confirm the correct next task.”

**Claude Code hardcodes a tax limit.**
Reply: “Rule 2 in CLAUDE.md says no hardcoded IRS limits. Move that value to `data/tax_constants_2025.json` and access via `tax_constants.get()`.”

**Tests are skipped or marked xfail.**
Reply: “A task is not done until its acceptance test passes (CLAUDE.md, How to work, step 3). Implement the test for real.”

**Claude Code wants to add a dependency not in the PRD/Build Plan.**
Ask why. Often the answer is reasonable; sometimes it isn’t. The PRD’s tech stack list is intentional.

## After v1

When all 37 tasks are done and the MVP is stable:

- Use the app for at least 4 weeks before considering changes.
- Track what you reach for that the app doesn’t have. Those become the v1.1 backlog.
- The FastAPI + React port (PRD §6.2 Phase 2) is the natural next step when you outgrow Streamlit’s single-user model — but only when, not before.

-----

*Built from `WealthCompass_PRD.md` and `WealthCompass_Build_Plan.md`. Refer back to those for any design question.*
