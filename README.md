# Harness Design — Website & App Builder

> **v1.1.0**

A Claude Code skill that turns a short idea into a polished website or app through Anthropic's **Planner → Generator → Evaluator** three-agent harness.

Based on [Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps) (Anthropic Engineering, 2026).

## What it does

You give it a topic or requirements. It runs an autonomous build loop:

```
User brief → Planner (spec.md) → Generator (build) → Evaluator (QA) → iterate until done
```

Each agent runs as an **independent subagent** with its own context window. They communicate only through files — no shared conversation, no self-grading.

## Why a harness?

| Problem | Without harness | With harness |
|---|---|---|
| **Self-evaluation bias** | Agent praises its own mediocre UI | Separate Evaluator grades adversarially |
| **Context anxiety** | Agent rushes to finish as context fills | Context resets with clean handoffs |
| **Generic "AI slop" design** | Template hero + 3-column + gradient blob | Design Quality & Originality weighted 2× in rubric |
| **Stub features** | Button exists but doesn't work | Evaluator probes end-to-end interaction |

## Key features

- **UI/UX optimized** — Planner outputs design tokens, information architecture, responsive strategy, accessibility requirements, user persona, and state inventory
- **Design system first** — Generator implements tokens as CSS variables before building components
- **9-axis rubric** — Design Quality (2×), Originality (2×), Craft, Functionality, Responsive, Accessibility, Interaction Design, Visual Hierarchy, UX Heuristics
- **Few-shot calibration anchors** — Score 1/3/5 examples per criterion ground the Evaluator's judgment from the first run
- **Differentiated hard thresholds** — 2×-weighted < 4 = FAIL, 1×-weighted < 3 = FAIL
- **Nielsen's 10 heuristics** built into Evaluator probes
- **Responsive testing** — Playwright at 375px / 768px / 1280px with screenshots
- **Accessibility checks** — WCAG AA contrast, keyboard nav, focus indicators, ARIA, axe-core
- **Direction-change control** — Generator can't pivot design without Evaluator's explicit `REDIRECT`; `design_memo.md` prevents context-reset amnesia
- **V1/V2 modes** — Full sprint loop (Sonnet) or simplified single-pass with 3–5 round cap (Opus)
- **Evaluator tuning workflow** — Read logs → find divergence → patch prompt → rerun cycle

## File handoffs

```
spec.md              Planner → Generator, Evaluator
sprint_contract.md   Generator ↔ Evaluator (negotiated)
generator_report.md  Generator → Evaluator
critique.md          Evaluator → Generator (includes REDIRECT authority)
design_memo.md       Generator → next session (prevents amnesia pivots)
handoff.md           Generator → next session (remaining work)
```

## Lessons from Anthropic's experiments

- **10th-iteration creative leap** — A Dutch museum site pivoted from dark landing page to 3D CSS perspective room on iteration 10. Late pivots can be breakthroughs, but must be rubric-justified.
- **Middle iterations sometimes best** — Final ≠ peak. Git commits on every increment let you roll back.
- **Prompting shapes character** — "Museum quality" made everything look like museums. Rubric describes qualities, not references.
- **Evaluator self-approves** — Untuned evaluators "talk themselves into" passing. Multiple tuning cycles required.
- **Core interactions get stubbed** — Buttons exist but don't work. "UI exists" ≠ "interaction works end-to-end."
- **Radical simplification failed** — Removing multiple harness components at once broke quality. One-at-a-time removal reveals what's load-bearing.

## Cost/time benchmarks (from article)

| Setup | Time | Cost | Result |
|---|---|---|---|
| Solo agent | ~20 min | ~$9 | Central feature broken |
| Full harness (V1) | ~6 hr | ~$200 | Polished, functional |
| Simplified harness (V2) | ~3 hr 50 min | ~$124.70 | 2+ hr coherent sessions |

## Installation

Copy the `harness-design/` folder to `~/.claude/skills/`.

## Usage

```
"Build a landing page for an AI writing tool"
"Design a dashboard for fleet management"
"Create a portfolio website with dark mode"
```

The skill activates automatically on website/app design requests.

## Changelog

### v1.1.0
- Add few-shot calibration anchors (score 1/3/5) for Design Quality, Originality, Craft, Functionality
- Add differentiated hard thresholds: 2× < 4 → FAIL, 1× < 3 → FAIL
- Add "Building Effective Agents" citation and simplicity principle
- Add "radical simplification failed" lesson
- Add 3–5 round cap to simplified single-session harness (PROMPT 5)

### v1.0.0
- Initial release with 9-axis rubric, UI/UX methodology, V1/V2 modes, article lessons

## License

MIT
