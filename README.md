# musk-algo

> A Claude Code slash command that applies **Elon Musk's 5-Step Algorithm** and **Eli Goldratt's Theory of Constraints** to any local repository - and gives you permission to delete.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugin-orange)](https://docs.claude.com/en/docs/claude-code)

---

## Install

```
/plugin marketplace add blutrich/musk-algo-plugin
/plugin install musk-algo
```

Then in any Claude Code session:

```
/musk-algo ~/projects/my-app
```

Or just `/musk-algo` and it'll ask for the path.

**Drop-in alternative** (no plugin system):

```bash
mkdir -p ~/.claude/commands
curl -L https://raw.githubusercontent.com/blutrich/musk-algo-plugin/main/commands/musk-algo.md \
  -o ~/.claude/commands/musk-algo.md
```

---

## What it does

Point `/musk-algo` at any repo path on your machine. It runs a three-phase analysis:

1. **Phase 0 - Intake.** Builds a mental model of the repo: type, languages, scale, activity, dominant file type. Detects prompt-heavy repos (Claude Code plugins, doc sites, skill libraries) and shifts to content-audit mode automatically.

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

## The origin story

### The climbing gym phone call

The idea for `/musk-algo` didn't come from a product meeting or a GitHub issue. It came from a late-night phone call at the climbing gym, between two friends — **[Isaac Zeevi](https://github.com/blutrich)** on one end, Ofer Blutrich on the other. The kind of call you take in the corner of the gym between burns, chalk still on your hands, where someone says "you know what would actually be useful…" and the idea lands hard enough that you walk out to your car and start building it.

Isaac was the one who connected the dots. He'd been carrying around two frames for a while and couldn't stop seeing how they applied to code:

- **Elon Musk's 5-Step Algorithm** from the Starship factory tour with Tim Dodd. *Make the requirements less dumb. Delete the part. Simplify or optimize. Accelerate cycle time. Automate.* In that order. The most important step is step 2. The second most important is step 1. Every engineer skips straight to step 3.
- **Eli Goldratt's Theory of Constraints** from *The Goal* (1984). Any system has exactly one bottleneck at a time. Any improvement anywhere else is an illusion — it looks like progress on a report, but the end-to-end system runs at the speed of the constraint.

Two frames. One says *delete before you fix*. The other says *fix the constraint, not the hot spots*. Isaac's insight: these aren't just operations philosophy — **they're exactly the two questions a code review tool should be asking, and nobody's tool was asking them**. Every code review tool ships a flood of style nits and low-signal warnings. None of them stops to ask whether the code should exist at all, and none of them distinguishes the one bottleneck from the ninety-nine hot spots.

"You should just build this," Isaac said. "Point it at a repo. Musk on one side, Goldratt on the other. See what falls out."

### The mess that made it urgent

Ofer had been in the trenches for a week, shipping Social Amplifier — a Claude Code plugin that turns busy Base44 employees into LinkedIn thought leaders without making them write anything. The v3 bundle had gone live. The Apify Feeder App was pumping 35 posts per run into a shared Slack channel. Champions were getting voice-matched drafts delivered to their DMs three times a week.

It worked. But the repo underneath it was a mess.

Not a catastrophic mess — a *lived-in* mess. Seven real install problems had been solved in order, each fix leaving a little scar: renamed concepts where some filenames caught up and some didn't, markdown scaffolds copied between skills that drifted apart, a README that ballooned to 601 lines because every new feature got a section appended to it, a 3.3 MB rendered video committed to git next to a pnpm-lock.yaml that shouldn't have been tracked either. The code worked. The repo didn't breathe.

Every builder knows that feeling. The point where you stop being able to see the shape of your own project because it's accumulated too much *stuff*. Isaac's phone call hit at exactly the moment Ofer needed the frame.

### The accidental ingredient

Earlier that same day, someone had dropped a `.skill` file in Ofer's Downloads: `repo-flow-analyzer.skill`. A Claude Code skill someone had packaged up — the kind of prototype that gets passed around Anthropic's internal community but never quite lands. Decent bones, no home.

After Isaac's call, the pieces clicked together:

1. Don't install the analyzer as a skill. Skills are ambient — they trigger on intent, and when they don't trigger, they fail silently. **Install it as a slash command.** Explicit. On-demand. Type `/`, pick from a menu, run the thing.
2. **User scope.** Not tied to any project. Available everywhere.
3. **Name it after the framing.** Not "repo analyzer". Not "code review". `musk-algo`. Because the name itself is the instruction.

Ofer typed the request: *"make it slash command user scope call it musk algo"*. Ten minutes later, `~/.claude/commands/musk-algo.md` existed.

### The smoke test

A tool you've never used is worthless until you run it once against something you know cold.

Ofer pointed it at `~/Downloads/Social-Amplifier-agent-main` — the Claude Code plugin version of Social Amplifier, the predecessor to the Superagent bundle that had shipped earlier in the week. He'd been in every corner of that repo. If the tool hallucinated, he'd catch it. If the tool surfaced something real, he'd feel it.

The report was — good. Not perfect. Good.

**The primary bottleneck it identified was exactly right.** `videos/install-demo/src/Demo.tsx`: a 658-line god file with 9 React components, all the theme constants, and the animation hooks inlined. Every edit forced scrolling past five scenes you weren't touching, and Remotion's studio re-evaluated the whole thing on every save. The tool's suggestion — split into `scenes/`, `components/`, `theme.ts`, then introduce a scene-manifest pattern — was the suggestion Ofer would have made himself, sized honestly at 30 minutes.

**The Delete First section found three things Ofer hadn't consciously noticed:**

1. The rendered `install-demo.mp4` (3.3 MB) committed to git. The `build` script regenerates it. Classic binary-in-git, doubles clone size forever.
2. A `cc10x-hook-events.log` file tracked in git from a different plugin's runtime. Would grow forever. Should have been gitignored from day one.
3. A test script named `tests/validate-new-champion-skill.sh` — a leftover from when the core concept in the repo was called "champion". He'd renamed everything to "hero" in commits `c1abde0` and `bf7175d`. Everything except this filename, and the header comment inside the file, which both still said "champion". A half-finished rename.

That last finding was the one that stung. Because Ofer had done the rename himself, and he'd been sure he'd gotten all of it. **The tool caught what his own eye had missed.** That's the moment you trust a tool — when it beats you at a thing you thought you'd finished.

### The three patches

The smoke test also showed the tool's gaps. Three real weaknesses that Ofer spotted by eye but the analyzer hadn't codified:

- The Phase 1 grep scans were all JS/TS-focused, so on a prompt-heavy repo (95% markdown) they came back empty. A Claude Code plugin analyzer that skips `.md` is useless.
- Tracked-artifact detection wasn't in the script. Ofer caught the binary and the log from Phase 0's "largest files" output, not from an explicit pass.
- Rename-drift detection wasn't a pass either. He'd found it by eye.

Three gaps, three patches:

1. **Repo-type detection in Phase 0.** Runs a file-extension histogram. If `.md` dominates, the command announces it's switching to a content-audit lens and treats prompts as the "code".
2. **Tracked-artifact scan.** `git ls-files | file` pass for binaries, plus a regex pass for logs, rendered output, caches. Feeds a mandatory sub-section in Delete First.
3. **Rename-drift scan.** Greps `git log` for rename commits, then scans filenames and docs for the old term. Reported as a Complexity Red Flag, not a deletion, because rename drift is something you *fix*, not something you remove.

All three patches shipped the same evening.

### The payoff, the same night

The normal arc of "I built a dev tool for myself" ends at "I built a dev tool for myself". You use it once, twice, on the repo you built it for, and then it sits in `~/.claude/commands/` gathering dust.

Ofer didn't do that. He took the report from the smoke test and he **acted on it**. That same evening:

- **Social-Amplifier-superagent** got commit `3ed3f39 chore(musk-algo week 1): scrub 14 stale references + add banned-terms lint`. Fourteen stale references surfaced by the analyzer, scrubbed in one pass. Plus a new banned-terms linter so the scrub couldn't regress.
- **base44-marketing-plugin** got commit `d2bb58a chore: remove dead weight — duplicate fonts, obsolete HTML artifacts, tighten gitignore`. Different repo, same Musk lens. Fonts bundled twice because two skills had independently pulled them in. HTML artifacts left over from a landing page experiment that shipped months ago and was never cleaned up.

Within hours of the analyzer's first run, two production repos were visibly cleaner. The commit messages cite the tool by name. That's the strongest signal a tool can give you — people *credit* it in git history. That's when you know it wasn't theater.

### Why it went to the community

Two things happened at once that pushed `/musk-algo` out the door.

**First, the tool was good enough.** After the three patches, it had produced a real report on a real repo, surfaced real issues that generated real commits. That's the bar. Not perfection. Evidence of impact on something that wasn't a toy example.

**Second, Isaac's framing deserved a larger audience than one builder at Base44.** The Claude Code community is full of builders who have their own accumulated messes, their own rename drifts, their own binary-in-git problems, their own god files they've been meaning to split up. A tool that gives them *permission to delete* — that frames cleanup work as a disciplined application of two well-known frameworks from two well-known thinkers — is a tool a community can rally around.

### The deeper reason

Every tool has a surface reason and a deeper reason. The surface reason for `/musk-algo` is: *Ofer had a messy repo and Isaac pointed out a frame.*

The deeper reason is this:

**AI builders accumulate mess faster than they can feel it.** The loop between "I had an idea" and "it's shipping in production" has collapsed to hours. The loop between "this accumulated mess is a problem" and "I notice the accumulated mess" has not collapsed. It's still weeks. Sometimes months. And in the gap between those two timescales, every Claude Code project drifts into incoherence — not from bad intent, from good velocity. You fix seven problems in a week and each fix leaves a scar and by Friday you can't see your own project anymore.

The tools we have for this are either too heavy (code review meetings, architecture reviews, RFCs) or too light (lint warnings, prettier runs). What was missing was a **30-second interrupt** you can fire at a repo when you feel the mess closing in, that applies two well-known frames, produces a short actionable report, and — crucially — gives you permission to delete.

The "permission to delete" part is the thing. Builders under velocity pressure hoard code. They hoard features, dependencies, files, abstractions, configs. Every line added has a sponsor; every line deleted has a critic. Musk's algorithm is famous not because the five steps are novel — they're not — but because it gives explicit, rehearsed permission to ask *should this exist at all?* Goldratt's Theory of Constraints does the same for effort allocation: it gives explicit permission to ignore 80% of what looks like a problem so you can fix the 20% that actually is.

Put those two permissions in a slash command. Ship it. Let the community use it. See what happens.

---

## What makes it different

Most repo analyzers are LOC-based and miss two huge waste sources:

1. **Tracked artifacts.** Binaries, rendered output, logs, caches committed to git. They bloat clones, pollute diffs, and rot fast. `musk-algo` runs `git ls-files | file` explicitly to catch these.

2. **Rename drift.** Filenames and docs that didn't get updated when a core concept was renamed. `musk-algo` greps `git log` for rename commits, then scans filenames for the old term.

It also handles **prompt-heavy repos** (Claude Code plugins, MCP servers, doc sites, skill libraries). Traditional scanners skip `.md` files; `musk-algo` treats prompts as the code when `.md` dominates.

---

## Principles (built into the prompt)

- **Never recommend adding complexity to fix complexity.** If a suggestion makes the codebase larger, it must be justified carefully.
- **Every recommendation must reference specific files, line numbers, or function names.** No vague advice.
- **The delete section comes before the fix section.** Always.
- **Be direct.** If the architecture is bad, say so. The user came for honest analysis, not softened findings.
- **Size effort honestly.** A "quick win" that takes 2 days is not a quick win.
- **Read the files, don't just scan them.** For anything flagged as complex, open the file and think.

---

## FAQ

**Q: Does it run anything destructive?**
No. It only reads: `find`, `grep`, `wc`, `git ls-files`, `git log`, `file`, `du`. No writes, no deletes, no git mutations. You decide what to delete based on the report.

**Q: Does it work on non-JS/TS repos?**
Yes. It handles JS/TS, Python, Go, Rust, Java, Ruby, and prompt-heavy markdown repos. Phase 0 detects the dominant file type and adjusts.

**Q: How big a repo can it handle?**
Tuned for repos under ~5K files. Large monorepos will work but the bash scans get slow; for those, point it at a specific subdirectory.

**Q: Will it analyze my monorepo's CI config, Docker, Makefile?**
Yes — Phase 1 Step 4-5 covers build/CI/test signals and flags manual steps in READMEs that could be scripted.

---

## Contributing

PRs welcome. Keep the prompt under 500 lines — the whole thing loads into context on every invocation. Structural improvements (new scan types, better bottleneck classification, language-specific detection) are higher value than wording tweaks.

---

## License

MIT. See [LICENSE](LICENSE).

---

## Credits

- **Isaac Zeevi** - the idea. Late-night phone call at the climbing gym. The two frames (Musk + Goldratt) and the insight that they're the two questions a code review tool should be asking but isn't. This tool wouldn't exist without that call.
- **Elon Musk's 5-Step Algorithm** - from the Starship factory tour with Tim Dodd (Everyday Astronaut).
- **Eli Goldratt's Theory of Constraints** - *The Goal* (1984).
- **Ofer Blutrich** - build, smoke test, patches, plugin packaging. Built April 14-15, 2026.
- **Claude Opus 4.6 (1M context)** - paired engineering throughout.
