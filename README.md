<!--
  ███╗   ███╗██╗   ██╗███████╗██╗  ██╗      █████╗ ██╗      ██████╗  ██████╗
  ████╗ ████║██║   ██║██╔════╝██║ ██╔╝     ██╔══██╗██║     ██╔════╝ ██╔═══██╗
  ██╔████╔██║██║   ██║███████╗█████╔╝█████╗███████║██║     ██║  ███╗██║   ██║
  ██║╚██╔╝██║██║   ██║╚════██║██╔═██╗╚════╝██╔══██║██║     ██║   ██║██║   ██║
  ██║ ╚═╝ ██║╚██████╔╝███████║██║  ██╗     ██║  ██║███████╗╚██████╔╝╚██████╔╝
  ╚═╝     ╚═╝ ╚═════╝ ╚══════╝╚═╝  ╚═╝     ╚═╝  ╚═╝╚══════╝ ╚═════╝  ╚═════╝
-->

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=12,14,16,18,20&height=200&section=header&text=musk-algo&fontSize=70&fontColor=ffffff&animation=fadeIn&fontAlignY=35&desc=Delete%20before%20you%20fix&descAlignY=60&descSize=22" width="100%"/>

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=500&size=22&duration=4000&pause=1000&color=4FC3F7&center=true&vCenter=true&multiline=true&repeat=false&width=760&height=80&lines=Musk%27s+5-step+algorithm+%2B+Goldratt%27s+Theory+of+Constraints;A+Claude+Code+slash+command+that+gives+you+permission+to+delete" alt="Typing" />

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugin-FF6B35?style=for-the-badge&logo=anthropic&logoColor=white)](https://docs.claude.com/en/docs/claude-code)
[![License: MIT](https://img.shields.io/badge/License-MIT-FFD700?style=for-the-badge)](https://opensource.org/licenses/MIT)
[![GitHub stars](https://img.shields.io/github/stars/blutrich/musk-algo-plugin?style=for-the-badge&logo=github&logoColor=white&labelColor=0D1117&color=4FC3F7)](https://github.com/blutrich/musk-algo-plugin/stargazers)
[![Built with Claude](https://img.shields.io/badge/Built%20with-Claude%20Opus%204.6-8B5CF6?style=for-the-badge&logo=anthropic&logoColor=white)](https://www.anthropic.com/claude)

</div>

<br/>

## <img src="https://media.giphy.com/media/iY8CRBdQXODJSCERIr/giphy.gif" width="30"> What it is

<img align="right" width="280" src="https://raw.githubusercontent.com/abhisheknaiidu/abhisheknaiidu/master/code.gif" />

```typescript
const muskAlgo = {
  command: "/musk-algo <repo-path>",
  frames: [
    "Musk's 5-step algorithm",
    "Goldratt's Theory of Constraints"
  ],
  asks: [
    "Should this code exist at all?",
    "What's the one bottleneck?"
  ],
  output: {
    deleteFirst: "binaries, logs, rename drift",
    bottleneck: "the one file slowing everything",
    roadmap: "Week 1 through Week 4",
    tone: "direct, specific, no vague advice"
  },
  runs: "in 30 seconds",
  permission: "to delete"
};
```

<br clear="right"/>

Point `/musk-algo` at any local repo. It runs a three-phase analysis — Musk on one side, Goldratt on the other — and produces a short, specific report you can act on the same afternoon.

It's the tool you reach for when a project you love has accumulated too much *stuff* and you've lost the shape of it.

<br/>

## <img src="https://media.giphy.com/media/WUlplcMpOCEmTGBtBW/giphy.gif" width="30"> Install

<div align="center">
<table>
<tr>
<td width="50%" valign="top">

### 🔌 Plugin marketplace

```
/plugin marketplace add blutrich/musk-algo-plugin
/plugin install musk-algo
```

Recommended. You get updates when I push them.

</td>
<td width="50%" valign="top">

### 📄 Drop-in file

```bash
mkdir -p ~/.claude/commands
curl -L https://raw.githubusercontent.com/blutrich/musk-algo-plugin/main/plugins/musk-algo/commands/musk-algo.md \
  -o ~/.claude/commands/musk-algo.md
```

One file. No dependencies. Works offline.

</td>
</tr>
</table>
</div>

Then in any Claude Code session:

```
/musk-algo ~/projects/my-app
```

Or just `/musk-algo` and it'll ask for the path.

<br/>

## <img src="https://media.giphy.com/media/VgCDAzcKvsR6OM0uWg/giphy.gif" width="30"> The origin story

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=rect&color=gradient&customColorList=2,14&height=2&width=100%25" />

</div>

### A phone call from the climbing gym

The idea didn't come from a product meeting or a GitHub issue. It came from a late-night phone call at the climbing gym — my friend **Isaac Zeevi** on one end, me on the other. The kind of call you take in the corner of the gym between burns, chalk still on your hands, where someone says *"you know what would actually be useful..."* and the idea lands hard enough that you walk out to your car and start building it.

Isaac had been carrying around two frames for a while and couldn't stop seeing how they applied to code:

<table>
<tr>
<td width="50%" valign="top" align="center">

### 🚀 Musk's 5-Step Algorithm
<sub>from the Starship factory tour with Tim Dodd</sub>

1. Make the requirements less dumb
2. **Delete the part**
3. Simplify or optimize
4. Accelerate cycle time
5. Automate

*The most important step is #2. The second most important is #1. Every engineer skips straight to #3.*

</td>
<td width="50%" valign="top" align="center">

### 🎯 Goldratt's Theory of Constraints
<sub>from *The Goal* (1984)</sub>

1. **Identify** the constraint
2. **Exploit** it
3. **Subordinate** everything else to it
4. **Elevate** it
5. **Repeat**

*Any system has exactly one bottleneck at a time. Any improvement anywhere else is an illusion.*

</td>
</tr>
</table>

Two frames. One says **delete before you fix**. The other says **fix the constraint, not the hot spots**.

Isaac's insight: these aren't just operations philosophy. **They're the two questions a code review tool should be asking, and nobody's tool was asking them.** Every code review tool ships a flood of style nits and low-signal warnings. None of them stops to ask whether the code should exist at all. None of them distinguishes the one bottleneck from the ninety-nine hot spots.

> *"You should just build this,"* Isaac said. *"Point it at a repo. Musk on one side, Goldratt on the other. See what falls out."*

### The mess that made it urgent

A week in the trenches on a fast-moving project had left a familiar residue — not a catastrophic mess, a *lived-in* mess. Real problems solved in sequence, each fix leaving a little scar. Renamed concepts where some filenames caught up and some didn't. Scaffolds copied between modules that drifted apart. READMEs that ballooned because every new feature got a section appended. Rendered artifacts committed next to lockfiles that shouldn't have been tracked either.

The code worked. The repo didn't breathe.

Every builder knows that feeling. The point where you stop being able to see the shape of your own project because it's accumulated too much *stuff*.

Isaac's two frames hit exactly that shape of problem. Not "write cleaner code next time" — *here's how to metabolize the mess you already have*.

### The earlier prototype

This wasn't the first attempt. Earlier that same day I'd already taken a stab at the idea — built in **Claude.ai**, the web chat interface, and exported as a `.skill` file (that's how Claude.ai hands skills off to the outside world). The download landed in my Downloads folder and sat there, unused. The bones were decent. The instincts were right. But the shape was wrong: a skill has to wait to be invited, and a tool you reach for when the mess is closing in needs to be on-demand, not ambient.

Isaac's phone call reframed what to do with it. The analyzer logic was fine — the *packaging* was wrong. Take the guts of the Claude.ai prototype, throw away the skill wrapper, rebuild it in Claude Code as a slash command I could actually *run* on demand.

### The smoke test

A tool you've never used is worthless until you run it once against something you know cold.

I pointed it at a repo I'd been in every corner of. If the tool hallucinated, I'd catch it. If the tool surfaced something real, I'd feel it.

The report was — honestly — good. Not perfect. Good.

<table>
<tr>
<td>

**🎯 The primary bottleneck it identified was the right one.**

A single file approaching ~700 lines that held nine components, a theme layer, and animation hooks all inlined together. Every edit forced scrolling past unrelated sections. The tool's recommendation — split into scenes, components, and a theme module, then introduce a manifest pattern — was exactly what I would have suggested myself, sized honestly at 30 minutes.

</td>
</tr>
<tr>
<td>

**🗑️ The Delete First section found three things I hadn't consciously noticed:**

1. A rendered binary artifact committed to git when a build script already regenerated it
2. A runtime log file tracked in git that would grow forever
3. A test script whose filename still carried a concept I'd renamed across the rest of the repo in a recent commit — the rename was 95% complete, this file and its header comment were the stragglers

That last finding was the one that stung. I'd done the rename myself and been certain I'd caught all of it. **The tool caught what my own eye had missed.** That's the moment you trust a tool — when it beats you at a thing you thought you'd finished.

</td>
</tr>
</table>

### The payoff, the same night

The normal arc of *"I built a dev tool for myself"* ends at *"I built a dev tool for myself"*. You use it once, twice, on the repo you built it for, and then it sits in `~/.claude/commands/` gathering dust.

That's not what happened.

The report got acted on that same evening. **Two separate repos got cleanup commits citing the tool by name.** Stale references scrubbed in one pass. Duplicate dependencies removed. A new linter added so the cleanup couldn't regress.

The analyzer's name showed up in git history as the reason the work happened.

> **That's the signal.** When people cite a tool in commit messages, it isn't theater. It's a tool people are actually using to metabolize their mess.

<br/>

## <img src="https://media.giphy.com/media/M9gbBd9nbDrOTu1Mqx/giphy.gif" width="30"> What makes it different

Most repo analyzers are LOC-based and miss two huge waste sources.

<table>
<tr>
<td width="33%" valign="top" align="center">

### 📦 Tracked artifacts

Binaries, rendered output, logs, caches committed to git.

They bloat clones, pollute diffs, and rot fast. `musk-algo` runs `git ls-files | file` explicitly to catch them.

`Delete First` `git hygiene`

</td>
<td width="33%" valign="top" align="center">

### 🔄 Rename drift

Filenames and docs that didn't get updated when a core concept was renamed.

`musk-algo` greps `git log` for rename commits, then scans filenames for the old term.

`Cognitive load` `Consistency`

</td>
<td width="33%" valign="top" align="center">

### 📝 Prompt-heavy repos

Claude Code plugins, MCP servers, doc sites, skill libraries.

Traditional scanners skip `.md` files. `musk-algo` treats prompts as the code when markdown dominates.

`AI-native` `Content audit`

</td>
</tr>
</table>

<br/>

## <img src="https://media.giphy.com/media/du3J3cXyzhj75IOgvA/giphy.gif" width="30"> The three phases

```
┌─────────────┐     ┌──────────────────┐     ┌────────────────┐     ┌──────────────┐
│  Phase 0    │────▶│  Phase 1         │────▶│  Phase 2       │────▶│  Phase 3     │
│  Intake     │     │  Musk's Algorithm│     │  Theory of     │     │  The Report  │
│             │     │                  │     │  Constraints   │     │              │
│ • Structure │     │ • Requirements   │     │ • Classify     │     │ • Overview   │
│ • Languages │     │ • Dead code      │     │   bottleneck   │     │ • Bottleneck │
│ • Scale     │     │ • Tracked        │     │ • 5 focusing   │     │ • Delete     │
│ • Dominant  │     │   artifacts      │     │   steps        │     │ • Complexity │
│   file type │     │ • Rename drift   │     │ • Identify →   │     │ • Roadmap    │
│             │     │ • Complexity     │     │   Exploit →    │     │              │
│             │     │ • Automation     │     │   Elevate      │     │              │
└─────────────┘     └──────────────────┘     └────────────────┘     └──────────────┘
```

Every recommendation references specific files, line numbers, or function names. No vague advice.

<br/>

## <img src="https://media.giphy.com/media/ZVik7pBtu9dNS/giphy.gif" width="30"> Principles built into the prompt

<table>
<tr>
<td>

🚫 **Never recommend adding complexity to fix complexity.** If a suggestion makes the codebase larger, it must be justified carefully.

📍 **Every recommendation references specific files, line numbers, or function names.** No vague advice.

🗑️ **The delete section comes before the fix section.** Always.

🗣️ **Be direct.** If the architecture is bad, say so. The user came for honest analysis, not softened findings.

⏱️ **Size effort honestly.** A "quick win" that takes 2 days is not a quick win.

📖 **Read the files, don't just scan them.** For anything flagged as complex, open the file and think.

</td>
</tr>
</table>

<br/>

## <img src="https://media.giphy.com/media/LnQjpWaON8nhr21vNW/giphy.gif" width="25"> FAQ

<details>
<summary><b>Does it run anything destructive?</b></summary>

No. It only reads: `find`, `grep`, `wc`, `git ls-files`, `git log`, `file`, `du`. No writes, no deletes, no git mutations. You decide what to delete based on the report.
</details>

<details>
<summary><b>Does it work on non-JS/TS repos?</b></summary>

Yes. It handles JS/TS, Python, Go, Rust, Java, Ruby, and prompt-heavy markdown repos. Phase 0 detects the dominant file type and adjusts.
</details>

<details>
<summary><b>How big a repo can it handle?</b></summary>

Tuned for repos under ~5K files. Large monorepos will work but the bash scans get slow; for those, point it at a specific subdirectory.
</details>

<details>
<summary><b>Will it analyze my monorepo's CI config, Docker, Makefile?</b></summary>

Yes — Phase 1 Step 4-5 covers build/CI/test signals and flags manual steps in READMEs that could be scripted.
</details>

<details>
<summary><b>Can I use this outside Claude Code?</b></summary>

The command is a markdown file with bash snippets — the logic is portable. But the execution model (a single slash command that orchestrates multi-step analysis with file reads in between) is built for Claude Code. Anywhere else you'd have to reimplement the shell around it.
</details>

<br/>

## <img src="https://media.giphy.com/media/hvRJCLFzcasrR4ia7z/giphy.gif" width="30"> Contributing

PRs welcome. A few notes:

- **Keep the prompt under 500 lines.** The whole thing loads into context on every invocation.
- **Structural improvements beat wording tweaks.** New scan types, better bottleneck classification, language-specific detection — these are the high-value contributions.
- **Smoke-test your changes on a real repo before opening a PR.** Include the repo (or a link to it) in the PR description.

<br/>

## <img src="https://media.giphy.com/media/ZDVePxWRhx0Fh7H9uA/giphy.gif" width="25"> License

MIT. See [LICENSE](LICENSE).

<br/>

## <img src="https://media.giphy.com/media/QssGEmpkyEOhBCb7e1/giphy.gif" width="30"> Credits

<table>
<tr>
<td width="50%" valign="top">

### 👥 The humans

**Isaac Zeevi** — the idea.
Late-night phone call at the climbing gym. The two frames (Musk + Goldratt) and the insight that they're the two questions a code review tool should be asking but isn't. *This tool wouldn't exist without that call.*

**[Ofer Blutrich](https://github.com/blutrich)** — build, smoke test, patches, plugin packaging.

</td>
<td width="50%" valign="top">

### 📚 The frameworks

**Elon Musk's 5-Step Algorithm**
From the Starship factory tour with Tim Dodd (Everyday Astronaut).

**Eli Goldratt's Theory of Constraints**
*The Goal* (1984) — the book every builder should read at least once.

**Claude Opus 4.6 (1M context)**
Paired engineering throughout.

</td>
</tr>
</table>

<br/>

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=12,14,16,18,20&height=120&section=footer" width="100%"/>

**Before you optimize it, ask whether it should exist at all.**

<sub>Built in a single evening. Named after the framing, because the name is the instruction.</sub>

</div>
