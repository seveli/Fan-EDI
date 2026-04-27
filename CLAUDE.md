# WealthCompass — Claude Code Project Instructions

This file is auto-loaded by Claude Code at the start of every session. Keep it short and high-signal.

-----

## What this project is

WealthCompass is a **local-first personal financial advisor MVP**. Streamlit UI, SQLite storage, pure-Python domain logic, optional Anthropic API for the AI advisor (offline-capable for everything else).

## Source of truth

- **PRD:** `docs/WealthCompass_PRD.md` — what and why (product design, schema, formulas).
- **Build Plan:** `docs/WealthCompass_Build_Plan.md` — how and in what order (37 numbered tasks across 7 phases).

**Implement tasks in the order specified by the Build Plan.** Do not skip ahead. Do not start with UI.

## How to work

1. Read the next un-done task from the Build Plan (Phase A → G, Task 1 → 37).
1. Implement everything specified in that task, including the tests in its acceptance criteria.
1. Run `make test && make lint && make typecheck`. All three must pass before claiming the task done.
1. Show the diff and test output. **Stop and wait for the user to say “next” before starting the following task.**
1. If anything in the docs is ambiguous or contradictory, **ask before guessing.**

## Architectural rules (load-bearing — do not violate)

1. **`src/wealthcompass/domain/` is pure.** No DB access, no HTTP, no file I/O, no `datetime.now()`, no `print`. Inputs and outputs are dataclasses. Domain modules may import only from `domain.types` and the standard library. This is enforced by `import-linter` in CI.
1. **No hardcoded IRS limits, contribution caps, tax brackets, phase-outs, or HSA thresholds — ever.** All such values come from the `tax_constants` table, seeded from `data/tax_constants_<year>.json`. If a constant is missing, raise `MissingTaxConstantError` — do not silently default.
1. **Decimal for money, always.** Never `float` for currency. Use `decimal.Decimal` in code; `Numeric(18, 2)` for money columns and `Numeric(28, 8)` for share quantities in the DB.
1. **Disclaimer everywhere.** Every Streamlit page calls `render_disclaimer_banner()` at the top and `render_disclaimer_footer()` at the bottom. Every PDF/Excel export contains the disclaimer on the cover page.
1. **Layering enforced.** Pages → services → domain → db. No skipping layers; no reverse imports. `import-linter` config in `.importlinter` enforces this.
1. **AI advisor outputs are structured.** Every recommendation block must contain FACTS / ASSUMPTIONS / SUGGESTION / TRADEOFFS / TAX / LIQUIDITY / RISK / HORIZON / CONFIDENCE. See PRD §10 for the prompt and Build Plan §8 for templates. Never promise specific returns. Never recommend single stocks as a wealth-building thesis.

## Coding conventions

- **Python 3.11+** with type hints everywhere.
- `mypy --strict` clean on `domain/` and `services/`.
- Format with `ruff format`. Lint with `ruff check`.
- Test files mirror source: `src/wealthcompass/domain/foo.py` ↔ `tests/domain/test_foo.py`.
- Property-based tests with Hypothesis for invariants (see Build Plan §9.3).
- Commit messages use conventional commits AND cite the task number, e.g. `feat: implement contribution waterfall (Task 8)`.
- One PR / commit per Build Plan task.

## How to run

```bash
make install        # install deps
make db-migrate     # alembic upgrade head
make db-seed        # seed tax_constants from data/tax_constants_2025.json
make db-seed-demo   # optional: load synthetic demo user
make run            # streamlit run app.py
make test           # pytest with coverage gate
make lint           # ruff check
make typecheck      # mypy --strict
```

## What NOT to do

- ❌ Do not start with UI scaffolding before domain logic exists.
- ❌ Do not invent IRS limits, brackets, or phase-outs from training data — always go through `tax_constants.get()`.
- ❌ Do not skip tests “for now” — a task is not done until its acceptance test passes.
- ❌ Do not bypass the layering rules even for “convenience.”
- ❌ Do not promise specific returns, recommend single stocks, or guarantee retirement outcomes in any AI output.
- ❌ Do not commit secrets. `.env` is gitignored; `.env.example` is the template.

## When in doubt

Cite the PRD section number or Build Plan task number in your reasoning. If the docs don’t address something, ask the user — don’t guess on financial logic.
