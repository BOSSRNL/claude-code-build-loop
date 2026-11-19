# Claude Code Build Loop

**Three AI "employees" that build, review, and QA your work before you ever see it.**

`loop-engineering` is a [Claude Code skill](https://docs.claude.com/en/docs/claude-code/skills) I use every day to ship automations, scripts, web apps, and data work. Instead of one AI writing code and calling it done, the work goes through a small team:

```
You (the Boss) ask for something
        ↓
[Employee 1: Builder]      builds it, runs it, checks its own work
        ↓          ↑ numbered findings, each with a severity
[Employee 2: Analyzer]     fresh-eyes review, loops with E1
        ↓
[Employee 3: Team Leader]  strict final gate, re-runs the critical path itself
        ↓ zero CRITICAL/HIGH findings
You get it, with proof that it works and how to undo it
```

Employees 2 and 3 run as **separate subagents**, so they review without any attachment to the work. The builder never grades its own homework.

## Why it works

- **No "it should work."** Every claim needs real output: a command that ran, a test count, a screenshot. Anything that could not be run is labelled UNVERIFIED.
- **A mistake registry that grows.** 115 real traps, each with a rule and a way to check it. Every new bug becomes a new entry, so the loop gets stricter every time it runs.
- **Only the relevant rules.** Registry entries are tagged (`core`, `ui`, `data`, `test`, `ops`, `security`, `env`). Every run checks `core` plus the tags that match the task, not all 115.
- **Severity, not nitpick loops.** Findings are CRITICAL, HIGH, MEDIUM, or LOW. The loop ends when no CRITICAL or HIGH is open; MEDIUM items are fixed or listed for you. Repeating a registry mistake is always at least HIGH, so the bar does not drop.
- **Right-sized.** Lite, Standard, and Full tiers. A typo in unshipped work gets a light pass; anything touching money, live data, or production gets the full loop, two analyzers (one on a different model), and a rollback plan.
- **You stay in control.** Anything destructive or public (deleting, publishing, sending) waits for your yes.

## What's inside

```
skills/loop-engineering/
├── SKILL.md                         the loop: roles, tiers, severity, checklists, report format
└── references/
    ├── mistake-registry.md          115 tagged never-again traps
    ├── evidence-playbooks.md        what "done" means for code, scripts, automations, APIs, data, and UI
    └── motion-system.md             animation tokens and choreography rules (used only when something animates)
```

## Install

1. Copy `skills/loop-engineering` into your Claude Code skills folder:
   - Windows: `C:\Users\<you>\.claude\skills\`
   - macOS / Linux: `~/.claude/skills/`
2. Restart Claude Code.
3. Ask for something and say **"loop engineering"**.

It works with plain Claude Code. If you have specialist reviewer agents installed (`code-reviewer`, `security-reviewer`, `gan-evaluator`), the loop uses them; if not, it runs the built-in `general-purpose` agent with the same checklist.

## Make it yours

- **Grow your own registry.** Keep the entries that fit your stack, delete the rest, and add a tagged entry after every mistake. The registry is where the value grows.
- **Adjust the `env` entries.** They were written on Windows 11 with PowerShell and Git Bash. Swap them for your own machine's quirks.
- **"The Boss"** is you. The skill addresses you that way so the AI keeps clear who approves what.

## Related

Building e-commerce funnels on Webcake? See [webcake-funnel-loop](https://github.com/BOSSRNL/webcake-funnel-loop), the same three-employee loop specialised for landing pages, Pancake POS pricing, Botcake follow-ups, and Meta Pixel tracking.

## License

MIT. Use it, fork it, adapt it. A link back is appreciated.

---

Built by [Rannelle](https://github.com/BOSSRNL) · Builder of e-commerce funnels & AI automations 🇵🇭
