---
description: Analyze a local repo through Musk's 5-Step Algorithm and Goldratt's Theory of Constraints to find bottlenecks, dead code, and over-engineering.
argument-hint: <repo-path>
---

# Musk Algo — Repo Flow Analyzer

Analyze the repo at `$ARGUMENTS` through two lenses:
1. **Musk's 5-Step Algorithm** — find what shouldn't exist at all
2. **Theory of Constraints (Goldratt)** — find what's slowing the entire system

Produce a deep, specific, actionable report with before/after examples and a prioritized roadmap. If `$ARGUMENTS` is empty, ask the user which repo path to analyze.

---

## Phase 0: Repo Intake

Build a map of the repo before diving in:

```bash
# Structure
find <repo> -type f | grep -v node_modules | grep -v .git | grep -v dist | grep -v __pycache__ | head -100

# LOC by file
find <repo> -type f | grep -v node_modules | grep -v .git | xargs wc -l 2>/dev/null | sort -rn | head -30

# Dependency files
find <repo> -name "package.json" -o -name "requirements.txt" -o -name "Cargo.toml" -o -name "pom.xml" -o -name "go.mod" | grep -v node_modules

# Git depth
cd <repo> && git log --oneline | wc -l 2>/dev/null

# Largest files
find <repo> -type f -not -path "*/node_modules/*" -not -path "*/.git/*" | xargs ls -la 2>/dev/null | sort -k5 -rn | head -20
```

Build a mental model: project type, languages, scale (LOC, files, deps), activity (commits).

### Detect repo type before choosing the scan depth

Some repos are 95% code. Others (Claude Code plugins, prompt libraries, documentation sites) are 95% markdown with a small code sidecar. The Musk/Goldratt lens still applies to prompt-heavy repos, but the scans in Phase 1 need to shift:

```bash
# What's the dominant file type?
find <repo> -type f -not -path "*/node_modules/*" -not -path "*/.git/*" -not -path "*/dist/*" \
  | sed 's/.*\.//' | sort | uniq -c | sort -rn | head -10
```

If `.md` dominates, treat prompts/skills as the "code" — scan them for stale references, half-finished renames, duplicated instructions, and sections that no longer match the current architecture. Tell the user explicitly: "This is a prompt-heavy repo, I'm shifting to a content-audit lens."

### Tracked-artifact scan (things that shouldn't be in git at all)

This is a common waste source that gets missed by LOC-based scans.

```bash
# Binary files checked into git (should almost always be in .gitignore)
cd <repo> && git ls-files 2>/dev/null | while read f; do
  [ -f "$f" ] && file "$f" 2>/dev/null | grep -qE "executable|binary|video|audio|archive|image data" && echo "BINARY: $f ($(du -h "$f" | cut -f1))"
done | head -20

# Log files, rendered output, caches tracked in git
git ls-files 2>/dev/null | grep -E "\.log$|\.cache$|/out/|/dist/|/build/|\.DS_Store|node_modules/" | head -20
```

Report these as **Delete First** items — they bloat clones, pollute diffs, and rot fast.

### Rename-drift scan (half-finished renames)

When a repo renames a core concept (`champion → hero`, `user → account`, `task → job`), the rename is almost never 100% complete. Stragglers in filenames, test scripts, and doc sections create cognitive overhead for every future reader.

```bash
# Find recent rename commits
cd <repo> && git log --oneline -20 2>/dev/null | grep -iE "rename|renam"

# If a rename was found, pick the old + new term and scan both to find stragglers
# grep -rn "OLD_TERM" <repo> --include="*.md" --include="*.sh" --include="*.json" | grep -v node_modules
# Also scan filenames — these are the most common offender
find <repo> -type f | grep -v node_modules | grep -v .git | grep -i "OLD_TERM"
```

Report these as **Complexity Red Flags** (not deletions) — they need fixing, not removing.

---

## Phase 1: Musk's Algorithm — "Should this exist?"

### Step 1: Requirements Audit (make requirements less dumb)

Look for code that exists because of an assumption nobody questioned.

```bash
# Include markdown and shell so prompt-heavy repos aren't skipped
grep -rn "TODO\|FIXME\|HACK\|XXX\|TEMP\|TEMPORARY" <repo> --include="*.js" --include="*.ts" --include="*.py" --include="*.go" --include="*.rb" --include="*.java" --include="*.rs" --include="*.md" --include="*.sh" | grep -v node_modules
grep -rn "featureFlag\|feature_flag\|isEnabled\|ENABLE_\|DISABLE_" <repo> --include="*.js" --include="*.ts" --include="*.py" | grep -v node_modules | head -20

# For prompt-heavy repos, also look for stale references
grep -rn "DEPRECATED\|LEGACY\|OBSOLETE" <repo> --include="*.md" | grep -v node_modules | head -20
```

Report as: "This code exists because of [assumed requirement]. If that requirement is no longer valid, [X lines / Y files] can be deleted."

### Step 2: Dead Code & Unused Assets (delete the part)

```bash
# Unused exports (JS/TS)
grep -rn "export " <repo> --include="*.ts" --include="*.js" | grep -v node_modules | grep -v ".test." | head -50

# Files never imported
find <repo> -name "*.ts" -o -name "*.js" | grep -v node_modules | grep -v ".test." | while read f; do
  base=$(basename "$f" | sed 's/\.[^.]*$//')
  count=$(grep -rn "import.*$base\|require.*$base" <repo> --include="*.ts" --include="*.js" | grep -v node_modules | grep -v "$f" | wc -l)
  [ "$count" -eq 0 ] && echo "UNUSED? $f"
done 2>/dev/null | head -20

# Dependencies
cat <repo>/package.json 2>/dev/null | grep -A100 '"dependencies"' | head -40

# Dead routes
grep -rn "router\.\|app\.get\|app\.post\|app\.put\|app\.delete\|@app\.route\|@router\." <repo> --include="*.js" --include="*.ts" --include="*.py" | grep -v node_modules | head -30
```

Report as: "File X is never imported. Dependency Y appears in package.json but has 0 usages. Route Z has no callers."

### Step 3: Over-Engineering & Unnecessary Complexity (simplify)

```bash
# Long files
find <repo> -type f \( -name "*.js" -o -name "*.ts" -o -name "*.py" \) | grep -v node_modules | xargs wc -l 2>/dev/null | sort -rn | head -15

# Deep nesting
grep -rn "if.*if.*if\|then.*then.*then" <repo> --include="*.js" --include="*.ts" | grep -v node_modules | head -20

# Wrapper abstractions that might not earn their keep
grep -rn "class.*Service\|class.*Manager\|class.*Handler\|class.*Helper\|class.*Util" <repo> --include="*.ts" --include="*.js" --include="*.java" | grep -v node_modules | head -20

# Config sprawl
find <repo> -name "*.config.*" -o -name "*.conf" -o -name "*.cfg" | grep -v node_modules | grep -v .git
```

For any file flagged as complex, **read it fully** and ask:
- Is this abstraction paying rent? Does it simplify the caller or just add a layer?
- Could this be 3 functions instead of a class with 12 methods?
- Is there a built-in/library function that already does this?

Report as: "Class X has 12 methods but only 2 are called externally. It could be 2 standalone functions, saving 80 lines."

### Steps 4 & 5: Acceleration & Automation

```bash
find <repo> -name "Makefile" -o -name "Dockerfile" -o -name ".github" -o -name "*.yml" -o -name "*.yaml" | grep -v node_modules | head -20

# Test coverage signal
find <repo> -name "*.test.*" -o -name "*.spec.*" -o -name "test_*.py" | grep -v node_modules | wc -l
find <repo> -name "*.ts" -o -name "*.js" -o -name "*.py" | grep -v node_modules | grep -v test | grep -v spec | wc -l

find <repo> -name "README*" | head -3
```

Report as: "Test coverage is X%. These critical paths have no tests: [list]. These manual README steps could be scripted: [list]."

---

## Phase 2: Theory of Constraints — "What's the bottleneck?"

Reconstruct the flow: Code Written → Tests → Build → Deploy → Runtime.

```bash
cat <repo>/package.json 2>/dev/null | grep -A20 '"scripts"'
cat <repo>/Makefile 2>/dev/null | head -40
du -sh <repo>/dist 2>/dev/null || du -sh <repo>/build 2>/dev/null
cat <repo>/package-lock.json 2>/dev/null | grep '"node_modules/' | wc -l
wc -l <repo>/requirements.txt 2>/dev/null
```

Classify the primary constraint:

| Constraint Type | Signals | Impact |
|-----------------|---------|--------|
| **Complexity Bottleneck** | Files >500 LOC, functions >100 lines, deep nesting | Every change is slow and risky |
| **Dependency Bottleneck** | 100+ deps, circular imports, tight coupling | Can't change A without breaking B |
| **Test Bottleneck** | <20% coverage, no CI, manual QA | Fear of shipping |
| **Architecture Bottleneck** | God files, mixed concerns, no clear layers | Can't onboard, can't scale |
| **Dead Weight Bottleneck** | >20% dead code, unused deps, legacy modules | Mental overhead, false complexity |

Then apply Goldratt's 5 focusing steps to the primary constraint:
1. **Identify** — name it precisely
2. **Exploit** — highest-leverage fix that doesn't require a rewrite
3. **Subordinate** — what else must change to support the fix
4. **Elevate** — longer-term architectural fix
5. **Repeat** — next bottleneck after this one is gone

---

## Phase 3: The Report

Structure output exactly as follows.

### 🔍 Repo Overview
- Type, languages, scale (LOC, files, deps)
- Age/activity from git
- Health score 1–10 with a one-line justification

### 🚨 Primary Bottleneck (Theory of Constraints)
- **What it is:** [specific name]
- **Why it's the constraint:** [evidence from code]
- **System-wide impact:** [what this slows down]
- **Exploit (quick win):** [specific action, specific file/function]
- **Elevate (proper fix):** [architectural recommendation]

### 🗑️ Delete First (Musk Step 2)
For each item:
- **What:** [file/function/dep/route/tracked-artifact]
- **Why it exists:** [original assumption]
- **Why it's safe to delete:** [evidence — no callers, no imports, binary in git, etc.]
- **Lines saved or repo size reduced:** [number]

Always include a "Tracked artifacts" sub-section if Phase 0 found binaries, logs, or rendered output in git. These are the easiest wins in any repo and get missed by pure LOC scans.

### ⚠️ Complexity Red Flags (Musk Step 3)
For each item:
- **File/Function:** [name + path]
- **Problem:** [specific issue]
- **Before:** [short snippet]
- **After:** [short snippet]
- **Why this matters:** [concrete benefit]

### 🔧 Specific Recommendations
Numbered list, ordered by impact:

1. **[Action verb] [specific thing]**
   - Why: [reason grounded in ToC or Musk]
   - How: [concrete steps]
   - Effort: Low / Medium / High
   - Impact: Low / Medium / High

### 🗺️ Roadmap

**Week 1 — Delete & Simplify (no new features)**
- [ ] Delete [specific files/deps]
- [ ] Refactor [specific function] from X lines to Y lines
- [ ] Remove [unused dependency]

**Week 2–3 — Fix the Bottleneck**
- [ ] [Specific architectural change]
- [ ] Add tests to [specific critical path]

**Week 4+ — Accelerate & Automate**
- [ ] [CI/CD improvement]
- [ ] [Automation of manual step]

---

## Principles

- **Never recommend adding complexity to fix complexity.** If a suggestion makes the codebase larger, justify carefully.
- **Every recommendation must reference specific files, line numbers, or function names.** No vague advice.
- **Delete section comes before the fix section.** Always.
- **Be direct.** If the architecture is bad, say so. The user came for honest analysis, not softened findings.
- **Size effort honestly.** A "quick win" that takes 2 days is not a quick win.
- **Read the files, don't just scan them.** For anything flagged as complex, read the actual content before recommending changes.
