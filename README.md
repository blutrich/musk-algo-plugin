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

The idea for `/musk-algo` didn't come from a product meeting or a GitHub issue. It came from a late-night phone call at the climbing gym, between two friends - **Isaac Zeevi** on one end, Ofer on the other. The kind of call you take in the corner of the gym between burns, chalk still on your hands, where someone says "you know what would actually be useful..." and the idea lands hard enough that you walk out to your car and start building it.

Isaac was the one who connected the dots. He'd been carrying around two frames for a while and couldn't stop seeing how they applied to code:

- **Elon Musk's 5-Step Algorithm** from the Starship factory tour with Tim Dodd. *Make the requirements less dumb. Delete the part. Simplify or optimize. Accelerate cycle time. Automate.* In that order. The most important step is step 2. The second most important is step 1. Every engineer skips straight to step 3.
- **Eli Goldratt's Theory of Constraints** from *The Goal* (1984). Any system has exactly one bottleneck at a time. Any improvement anywhere else is an illusion - it looks like progress on a report, but the end-to-end system runs at the speed of the constraint.

Two frames. One says *delete before you fix*. The other says *fix the constraint, not the hot spots*. Isaac's insight: these aren't just operations philosophy - **they're exactly the two questions a code review tool should be asking, and nobody's tool was asking them**. Every code review tool ships a flood of style nits and low-signal warnings. None of them stops to ask whether the code should exist at all, and none of them distinguishes the one bottleneck from the ninety-nine hot spots.

"You should just build this," Isaac said. "Point it at a repo. Musk on one side, Goldratt on the other. See what falls out."

### The mess that made it urgent

The call landed at exactly the right moment. A week in the trenches on a fast-moving project had left a familiar residue - not a catastrophic mess, a *lived-in* mess. Real problems solved in sequence, each fix leaving a little scar: renamed concepts where some filenames caught up and some didn't, scaffolds copied between modules that drifted apart, READMEs that ballooned because every new feature got a section appended, rendered artifacts committed next to lockfiles that shouldn't have been tracked either. The code worked. The repo didn't breathe.

Every builder knows that feeling. The point where you stop being able to see the shape of your own project because it's accumulated too much *stuff*.

Isaac's two frames hit exactly that shape of problem. Not "write cleaner code next time" - *here's how to metabolize the mess you already have*.

### The earlier prototype

This wasn't the first attempt. Earlier that same day, the author had already taken a stab at the idea - built in **Claude.ai**, the web chat interface, and exported as a `.skill` file (that's how Claude.ai hands skills off to the outside world). The download landed in the usual Downloads folder and sat there, unused. The bones were decent. The instincts were right. But the shape was wrong: a skill has to wait to be invited, and a tool you reach for when the mess is closing in needs to be on-demand, not ambient.

Isaac's phone call reframed what to do with it. The analyzer logic was fine - the *packaging* was wrong. Take the guts of the earlier Claude.ai prototype, throw away the skill wrapper, rebuild it in Claude Code as a slash command the user can actually *run* on demand. Different shape, same core. That's the move.

### Slash command, not a skill

The first real decision was format. An analyzer like this could have been packaged as an ambient "skill" - a capability that triggers when the assistant detects relevant intent in a conversation. Skills are powerful but fragile: when the trigger pattern is wrong (too narrow, too broad, too jargon-heavy) the skill never runs, and worse, nobody notices, because skills failing silently look identical to skills being unnecessary.

A slash command is the opposite. You type `/`, pick from a menu, and the thing runs. No ambiguity. No "did it trigger?" There is a command, you ran it, it produced output. That model maps cleanly onto what a builder actually wants when they feel the mess closing in: **I want to run the thing. Right now. On this repo.**

And there's a deeper reason. Slash commands travel as single files. Drop a markdown file into `~/.claude/commands/` and it works. No marketplace, no installer, no dependency tree. You can DM it, gist it, email it. That property - **a dev tool that travels as a single file** - is exactly what a tool meant to reach a community needs to be.

So: slash command. User scope. Available everywhere. Named after the framing, because the name itself is the instruction.

### The smoke test

A tool you've never used is worthless until you run it once against something you know cold.

The first test was a repo the author had been in every corner of - the kind of repo where any hallucination would be immediately obvious and any real finding would land hard. The analyzer walked its three phases and produced a report that was, honestly, good. Not perfect. Good.

**The primary bottleneck it identified was the right one.** A single file approaching ~700 lines that held nine components, a theme layer, and animation hooks all inlined together. Every edit forced scrolling past unrelated sections. The tool's recommendation - split the file into scenes, components, and a theme module, then introduce a manifest pattern - was exactly what the author would have suggested themselves, sized honestly at thirty minutes.

**The Delete First section found three things that had slipped past conscious notice:**

1. A rendered binary artifact committed to git when a build script already regenerated it.
2. A runtime log file tracked in git that would grow forever.
3. A test script whose filename still carried a concept that had been renamed across the rest of the repo in a recent commit. The rename was 95% complete; this file and its header comment were the stragglers.

That last finding was the one that stung. The author had done the rename and had been certain they'd caught all of it. **The tool caught what their own eye had missed.** That's the moment you trust a tool - when it beats you at a thing you thought you'd finished.

### The three patches

The smoke test also exposed three gaps - real weaknesses the author spotted by eye but the analyzer hadn't codified:

- The Phase 1 scans were all code-language-focused, so on a prompt-heavy repo (dominated by markdown) they came back empty. A modern repo analyzer that skips `.md` is useless for a whole category of project.
- Tracked-artifact detection wasn't a dedicated pass. The author caught binaries and logs from general "largest files" output, not from explicit intent.
- Rename-drift detection wasn't a pass either. They'd found it by eye.

Three gaps, three patches, applied the same session:

1. **Repo-type detection in Phase 0.** Runs a file-extension histogram. If markdown dominates, the command announces it's switching to a content-audit lens and treats prompts as the "code".
2. **Tracked-artifact scan.** A `git ls-files | file` pass for binaries, plus a regex pass for logs, rendered output, and caches. Feeds a mandatory sub-section in Delete First.
3. **Rename-drift scan.** Greps `git log` for rename commits, then scans filenames and docs for the old term. Reported as a Complexity Red Flag, not a deletion, because rename drift is something you *fix*, not something you remove.

### The payoff, the same night

The normal arc of "I built a dev tool for myself" ends at "I built a dev tool for myself". You use it once, twice, on the repo you built it for, and then it sits in `~/.claude/commands/` gathering dust.

That's not what happened. The report got acted on that same evening. Two separate repos got cleanup commits citing the tool by name. Stale references scrubbed in one pass. Duplicate dependencies removed. A new linter added so the cleanup couldn't regress. The analyzer's name showed up in git history as the reason the work happened.

**That's the signal.** When people cite a tool in commit messages, it isn't theater. It's a tool people are actually using to metabolize their mess.

### The deeper reason

Every tool has a surface reason and a deeper reason. The surface reason for `/musk-algo` is: *a friend pointed out a frame on a late-night phone call.*

The deeper reason is this:

**AI builders accumulate mess faster than they can feel it.** The loop between "I had an idea" and "it's shipping in production" has collapsed to hours. The loop between "this accumulated mess is a problem" and "I notice the accumulated mess" has not collapsed. It's still weeks. Sometimes months. And in the gap between those two timescales, every fast-moving project drifts into incoherence - not from bad intent, from good velocity. You fix seven problems in a week and each fix leaves a scar and by Friday you can't see your own project anymore.

The tools we have for this are either too heavy (code review meetings, architecture reviews, RFCs) or too light (lint warnings, prettier runs). What was missing was a **30-second interrupt** you can fire at a repo when you feel the mess closing in, that applies two well-known frames, produces a short actionable report, and - crucially - gives you permission to delete.

The "permission to delete" part is the thing. Builders under velocity pressure hoard code. They hoard features, dependencies, files, abstractions, configs. Every line added has a sponsor; every line deleted has a critic. Musk's algorithm is famous not because the five steps are novel - they're not - but because it gives explicit, rehearsed permission to ask *should this exist at all?* Goldratt's Theory of Constraints does the same for effort allocation: it gives explicit permission to ignore 80% of what looks like a problem so you can fix the 20% that actually is.

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
Yes - Phase 1 Step 4-5 covers build/CI/test signals and flags manual steps in READMEs that could be scripted.

---

## Contributing

PRs welcome. Keep the prompt under 500 lines - the whole thing loads into context on every invocation. Structural improvements (new scan types, better bottleneck classification, language-specific detection) are higher value than wording tweaks.

---

## License

MIT. See [LICENSE](LICENSE).

---

## Credits

- **Isaac Zeevi** - the idea. Late-night phone call at the climbing gym. The two frames (Musk + Goldratt) and the insight that they're the two questions a code review tool should be asking but isn't. This tool wouldn't exist without that call.
- **Elon Musk's 5-Step Algorithm** - from the Starship factory tour with Tim Dodd (Everyday Astronaut).
- **Eli Goldratt's Theory of Constraints** - *The Goal* (1984).
- **Ofer Blutrich** - build, smoke test, patches, plugin packaging.
- **Claude Opus 4.6 (1M context)** - paired engineering throughout.
