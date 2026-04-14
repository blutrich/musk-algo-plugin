# musk-algo

> A Claude Code slash command that applies **Elon Musk's 5-Step Algorithm** and **Eli Goldratt's Theory of Constraints** to any local repository - and gives you permission to delete.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugin-orange)](https://docs.claude.com/en/docs/claude-code)

---

## What it does

Point `/musk-algo` at any repo path on your machine. It runs a three-phase analysis:

1. **Phase 0 - Intake.** Builds a mental model of the repo: type, languages, scale, activity, dominant file type. Detects prompt-heavy repos (Claude Code plugins, doc sites) and shifts to content-audit mode automatically.

2. **Phase 1 - Musk's 5-Step Algorithm.** Asks "should this exist?" for every line:
   - Requirements audit (find code that exists because of an assumption nobody questioned)
   - Dead code and unused assets
   - Tracked-artifact scan (binaries, logs, rendered output checked into git)
   - Rename-drift scan (half-finished renames in filenames and docs)
   - Over-engineering and complexity
   - Acceleration and automation candidates

3. **Phase 2 - Theory of Constraints.** Classifies the primary bottleneck (complexity, dependency, test, architecture, or dead-weight) and applies Goldratt's five focusing steps to it.

4. **Phase 3 - The Report.** You get:
   - **Repo Overview** with a 1-10 health score
   - **Primary Bottleneck** with both a quick win (exploit) and a proper fix (elevate)
   - **Delete First** list with a "Tracked Artifacts" sub-section
   - **Complexity Red Flags** with before/after code snippets
   - **Specific Recommendations** ordered by impact
   - **Week 1-4 Roadmap**

Every recommendation references specific files, line numbers, or function names. No vague advice.

---

## Why it works

Most "code review" tools give you noise: a flood of style nits and low-signal warnings. This one asks two questions that actually matter:

- **Musk's question:** Should this code exist at all? (Delete before you fix.)
- **Goldratt's question:** What's the one thing slowing the entire system down? (Fix the constraint, not random hot spots.)

The output is a short list of high-leverage actions, not a 500-item backlog.

---

## Install

### Option A: Plugin marketplace (recommended)

```
/plugin marketplace add blutrich/musk-algo-plugin
/plugin install musk-algo
```

### Option B: Drop-in file

```bash
mkdir -p ~/.claude/commands
curl -L https://raw.githubusercontent.com/blutrich/musk-algo-plugin/main/commands/musk-algo.md \
  -o ~/.claude/commands/musk-algo.md
```

Then restart Claude Code. The `/musk-algo` command will be available.

---

## Usage

```
/musk-algo ~/projects/my-app
```

Or just:

```
/musk-algo
```

and it'll ask for the path.

---

## Example output (real run)

Run against a small Claude Code plugin repo (~50 files, mostly markdown + a 658-line TSX video component):

**Primary Bottleneck identified:**
> `videos/install-demo/src/Demo.tsx` - 658-line god file with 9 React components + theme constants all inlined. Every video revision forces you to scroll past every scene. Remotion studio hot-reloads the whole file, so tweaking subtitles in Scene 2 re-evaluates everything.
>
> **Exploit (30 min):** Split into `scenes/`, `components/`, `theme.ts`. `Demo.tsx` becomes a 30-line composition root.
>
> **Elevate:** Scene-manifest pattern - each scene exports `{component, durationInFrames, name}` and Demo.tsx maps over the manifest.

**Delete First (tracked artifacts):**
1. `videos/install-demo/out/install-demo.mp4` - 3.3 MB rendered binary in git. `build` script regenerates it.
2. `.claude/cc10x/v10/cc10x-hook-events.log` - runtime log, grows forever.

**Rename-drift caught:**
> Repo was renamed `champion -> hero` in commits `c1abde0` and `bf7175d`. But `tests/validate-new-champion-skill.sh` still carries the old name in its filename and header comment. Half-finished rename. 3-line fix.

**Within hours of the report, the user shipped commits:**
- `3ed3f39 chore(musk-algo week 1): scrub 14 stale references + add banned-terms lint`
- `d2bb58a chore: remove dead weight - duplicate fonts, obsolete HTML artifacts, tighten gitignore`

The tool's biggest value isn't the report - it's that it gives you permission to delete.

---

## What makes it different

Most repo analyzers are LOC-based and miss two huge waste sources:

1. **Tracked artifacts.** Binaries, rendered output, logs, caches committed to git. They bloat clones, pollute diffs, and rot fast. `musk-algo` runs `git ls-files | file` explicitly to catch these.

2. **Rename drift.** Filenames and docs that didn't get updated when a core concept was renamed. `musk-algo` greps `git log` for rename commits, then scans filenames for the old term.

It also handles **prompt-heavy repos** (Claude Code plugins, MCP servers, doc sites, skill libraries). Traditional scanners skip `.md` files; `musk-algo` treats prompts as the code when `.md` dominates.

---

## Principles (built into the prompt)

- Never recommend adding complexity to fix complexity. If a suggestion makes the codebase larger, it must be justified carefully.
- Every recommendation must reference specific files, line numbers, or function names. No vague advice.
- The delete section comes before the fix section. Always.
- Be direct. If the architecture is bad, say so. The user came for honest analysis, not softened findings.
- Size effort honestly. A "quick win" that takes 2 days is not a quick win.
- Read the files, don't just scan them.

---

## FAQ

**Q: Does it run anything destructive?**
No. It only reads: `find`, `grep`, `wc`, `git ls-files`, `git log`, `file`, `du`. No writes, no deletes, no git mutations. You decide what to delete based on the report.

**Q: Does it work on non-JS/TS repos?**
Yes. It handles JS/TS, Python, Go, Rust, Java, Ruby, and prompt-heavy markdown repos. Phase 0 detects the dominant file type and adjusts.

**Q: How big a repo can it handle?**
Tuned for repos under ~5K files. Large monorepos will work but the bash scans get slow; for those, point it at a specific subdirectory.

**Q: Will it analyze my monorepo's CI config, Docker, Makefile?**
Yes - Phase 1 Step 4-5 covers build/CI/test signals and flags manual steps in READMEs that could be scripted.

---

## Contributing

PRs welcome. Keep the prompt under 500 lines - the whole thing loads into context on every invocation. Structural improvements (new scan types, better bottleneck classification, language-specific detection) are higher value than wording tweaks.

---

## License

MIT. See [LICENSE](LICENSE).

---

## Credits

Framework credit:
- **Elon Musk's 5-Step Algorithm** - from the Starship factory tour with Tim Dodd (Everyday Astronaut)
- **Theory of Constraints** - Eli Goldratt, *The Goal* (1984)

Built by [Ofer Blutrich](https://github.com/blutrich) with Claude Opus 4.6.
