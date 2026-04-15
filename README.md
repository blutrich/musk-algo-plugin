# musk-algo

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugin-FF6B35?style=flat-square&logo=anthropic&logoColor=white)](https://docs.claude.com/en/docs/claude-code)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](https://opensource.org/licenses/MIT)

A Claude Code slash command that gives you permission to delete.

Runs Musk's 5-step algorithm and Goldratt's Theory of Constraints on a local repo. Outputs a short, specific report with a **Delete First** section, a named bottleneck, and a roadmap you can act on the same afternoon.

The hardest thing to get an LLM to do is recommend deletion. This command is built around that one job.

---

## Install

**Plugin marketplace (recommended):**
```
/plugin marketplace add blutrich/musk-algo-plugin
/plugin install musk-algo
```

**Drop-in file:**
```bash
mkdir -p ~/.claude/commands
curl -o ~/.claude/commands/musk-algo.md \
  https://raw.githubusercontent.com/blutrich/musk-algo-plugin/main/plugins/musk-algo/commands/musk-algo.md
```

---

## Usage

```
/musk-algo <path-to-repo>
```

Or just describe the problem in natural language:

- *"audit my repo, it feels slow to work in"*
- *"I inherited this codebase, what should I delete first?"*
- *"my Base44 app is getting messy, give me permission to clean it up"*
- *"every change in this repo takes forever, where's the bottleneck?"*

The command runs even if you can't name the problem. It's designed for the feeling of *"something is off here"* before you have vocabulary for it.

---

## What you get back

A structured report with six sections:

1. **Repo Overview** — scale, languages, health score
2. **Primary Bottleneck** — the one thing slowing everything else (ToC)
3. **Delete First** — files, dependencies, tracked binaries, dead routes, with line counts saved
4. **Complexity Red Flags** — before/after code snippets for over-engineered pieces
5. **Specific Recommendations** — ordered by impact, each with effort + impact ratings
6. **Roadmap** — Week 1 (delete) → Week 2-3 (fix bottleneck) → Week 4+ (accelerate)

Every recommendation references a specific file path or function name. No vague advice.

---

## Example output (anonymized)

> **Repo:** `ai-marketing-agent` — FastAPI + Python + 22 Claude Code skills
>
> **Primary Bottleneck:** Architecture — "Dual-Skills + Manual Router" pipeline.
> Every new skill requires 3 synchronized edits across 3 directories. The pipeline has already broken itself: 22 skills in `.claude/skills/`, 3 in `.agents/skills/`, 21 routed in `server/main.py`, 12 listed in `CLAUDE.md`. Four sources of truth, zero agreement.
>
> **Delete First:**
> - `.agents/skills/` (14% coverage, abandoned pattern) — ~200 LOC
> - `video/src/temp/` directory (`TempRoot.tsx`, `TestVideo.tsx`) — ~370 LOC
> - Duplicate Slack bot in `deploy-slack-bot/` — ~263 LOC
> - Duplicate PRD in `.agents/AI-Marketing-System-PRD.md`
>
> **Exploit (quick win):** Replace `build_prompt()`'s 26-entry `content_type_hints` dict (lines 127-152) with auto-discovery that reads `.claude/skills/*/SKILL.md` frontmatter at startup. After this, adding a skill collapses from 3 manual edits to 1 file drop.
>
> **Bottom line:** The repo does not feel slow because the code is slow. It feels slow because `CLAUDE.md` enforces a 3-step ritual that has already drifted, and there are zero tests, so every edit carries fear tax.

The user felt something was wrong. They couldn't name it. The command named it.

---

## The principles

The command follows three rules that matter:

1. **Delete First comes before Fix.** You can't improve what you should have removed. Reports always list deletions before refactors.
2. **Every recommendation references a specific file, function, or dependency.** No "consider refactoring the service layer." Either name the file or don't say it.
3. **Read files before recommending changes.** Scans produce leads, not verdicts. For anything flagged as complex, the command reads the actual content before suggesting anything.

See [`plugins/musk-algo/commands/musk-algo.md`](plugins/musk-algo/commands/musk-algo.md) for the full prompt.

---

## FAQ

**Does it work on prompt-heavy repos (docs, skills, plugins)?**
Yes. Phase 0 detects when markdown dominates and shifts to a content-audit lens — scanning for stale references, duplicated instructions, and rename drift instead of dead code.

**Can it run on a subdirectory instead of the whole repo?**
Yes. Point it at any path that has files.

**Does it modify my code?**
No. It only reads and reports. You decide what to delete.

**Will it work without Claude Opus?**
The prompt is model-agnostic. It's been tuned on Opus 4.6 and produces the most depth there, but the structure works on any model that can follow a multi-phase prompt.

---

## License

MIT. Use it, fork it, ship derivatives.
