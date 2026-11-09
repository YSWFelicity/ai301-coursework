# Contribution: --explain: show which dependency pulls in a gap package (uv.lock)

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Yingshu Wang
**GitHub Username:** YSWFelicity
**Issue:** https://github.com/patrickballard/wheelgap/issues/26
**Pull Request:** [Not opened yet]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Building and Refining an Open-Source Issue Scout**

I built an Issue Scout using a personalized fit profile and GitHub search strategies to identify beginner-friendly issues related to Python, Java, and backend development. During the initial scan, I noticed that the search results were heavily concentrated in a few repositories, so I added a ranking rule to limit the number of candidates from each repository. I also discovered that some promising issues already had open pull requests, even when they had no assignees or comments. This experience showed me the importance of checking claim signals and evaluating issue scope before choosing an open-source contribution.

**Unit 6: Setting Up Voice Guard and Improving Contribution Checks**

I installed Voice Guard and connected it to Claude Code to check outgoing GitHub comments against my voice guide. During the wiring test, the guard successfully blocked a GitHub comment command without a readable message body. I also updated the Issue Scout rubric with a scope-feasibility check and installed the broader scopes for Repro Check and Plan Check. My first claim draft was rejected because it promised an implementation before I had investigated the code, so I revised it to focus on investigation and a realistic progress update. The revised draft passed Repro Check and was successfully posted to wheelgap Issue #26. Before reproducing, I ran a scoped orientation of only the wheelgap subsystem my issue touches (`check.py`, `cli.py`, and the `--explain` tests and fixtures) and saved the conventions, build and test commands, and AI policy in a `CLAUDE.local.md` file, which is listed in `.git/info/exclude` so it stays out of Git and will never be committed upstream.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I used my Issue Scout to search for beginner-friendly open-source contributions that match my interests in Python, backend development, and algorithms. The scout identified wheelgap Issue #26 and accepted it as a potential contribution. The issue asks for a way to explain which dependency brings a particular package into a dependency graph generated from `uv.lock`. I chose it because the task has a defined scope and gives me an opportunity to apply breadth-first search (BFS) to a real software project.

During issue selection, I learned that an open issue is not necessarily available for contribution. Some candidates already had pull requests, and another issue I considered had been claimed by someone else. I therefore checked Issue #26's comments, assignees, and linked pull requests before claiming it. I also updated my Issue Scout rubric to evaluate scope feasibility more explicitly. One limitation of the scout is that its results can become outdated quickly, so I still need to verify availability manually before posting a claim.

**Claim Comment:** https://github.com/patrickballard/wheelgap/issues/26#issuecomment-6731154260

**Toolkit**

- Tool: Issue Scout, using my personalized search strategy and scope-feasibility rubric to evaluate open-source issues.
- What it produced: A scan of 300 issues and a shortlist of candidates, including wheelgap #26, which received an accept verdict.
- Difficulties: Some promising issues already had open pull requests or were claimed after the scan. The initial rubric also needed a clearer assessment of implementation difficulty.
- What changed: I patched the rubric to use `scope-feasible`, added a difficulty note, regraded three candidates, and manually verified Issue #26 before claiming it.

---

## Understanding the Issue

**Problem Description**

wheelgap's `--explain` flag already tells me *why* a package has no compatible wheel for a target, but it does not tell me *why that package is in my dependency graph at all*. When the gap package is a transitive dependency from `uv.lock`, I have no way to see which of my direct dependencies pulled it in, so I cannot tell which dependency I would need to change, pin, or replace. The missing piece is a dependency path ("via" chain) from a root package to the gap package.

**Expected Behavior**

For each gap package, `--explain` should show the dependency chain that makes it necessary. In text output this would be a line such as:

```text
winonly 311 on win-amd64: ✗ missing
  via: demo → winonly
```

With `--json`, the matching entry should carry the same information as a field, for example `"via": ["demo", "winonly"]`. When there are several paths, the issue asks for the shortest one, which is why a breadth-first search fits.

**Current Behavior**

Running `--explain` against the `uvproj` fixture reports the gap and the rejected wheel (`winonly-311-cp313-cp313-win_amd64.whl  built for cp313-cp313, not CPython 3.12`), but prints no `via:` line. The `--json` output includes package information, results, and rejected wheels, but no `via` field.

**Affected Components**

- `src/wheelgap/check.py`: `reachable()` walks the dependency edges but returns only a `set[int]` of reachable package indexes, so parent information is discarded. The traversal is depth-first, so it would not guarantee a shortest path even if parents were kept. `Row` holds per-package results and currently has nowhere to store a path.
- `src/wheelgap/cli.py`: `render_explain()` prints the text explanation and `report_json()` builds the JSON output; both can only show what `Row` carries.
- `tests/test_check.py` (including `test_uv_lock_graph`), `tests/test_explain.py`, and `tests/fixtures/uvproj/uv.lock`: the existing graph and `--explain` tests and the fixture I used for reproduction.

This is my understanding from reading the code before writing any fix; I have not yet confirmed how the change should handle multiple roots or dev-group dependencies.

**Toolkit**

- Tool: Claude Code, used for a scoped orientation of only the subsystem the issue touches (`check.py`, `cli.py`, the `--explain` tests, and the `uvproj` fixture) rather than the whole repository. I saved the result in a local `CLAUDE.local.md` that is excluded from Git through `.git/info/exclude`.
- What it produced: A short notes file listing the CONTRIBUTING.md conventions (tests with local fixtures, a CHANGELOG entry under `## Unreleased`, README update for user-facing flags, `Closes #26`), the build and test commands, the AI policy, and the key finding that `reachable()` returns only a set of indexes and loses parent information.
- Difficulties: An orientation can sound confident about code it only skimmed, so I had to check each claim against the source myself (for example, that `reachable()` is depth-first and returns `set[int]`). The scope also leaves open questions, such as how dev-group roots should appear in a path.
- What changed: I kept the orientation narrow on purpose, verified the function names and signatures by opening the files, and kept the notes local so they never end up in a commit or PR.

---

## Reproduction Process

**Environment Setup**

- OS: macOS (Darwin 27.2.0), arm64
- Python: 3.12.6
- Package manager: uv 0.13.7
- Project: wheelgap 0.2.0, baseline commit `56c0974`, on my local branch `issue-26-explain-dependencies`

I cloned wheelgap and created the branch. The first challenge was that uv was not installed on my machine, so the project's `uv run` commands failed. I installed it with Homebrew:

```bash
brew install uv
```

After that, I let uv set up the environment by running the test suite from the repository root:

```bash
uv run --group dev pytest
```

uv created the virtual environment and installed the dev group, and the baseline suite finished with **121 passed and 1 skipped**, so I started from a clean, working state.

**Steps to Reproduce**

From the wheelgap repository root:

1. Run the text explanation against the bundled fixture:

   ```bash
   uv run wheelgap tests/fixtures/uvproj -p 3.12 -t win-amd64 --explain --exit-zero
   ```

2. Inspect the explanation for `winonly 311`. Observed:

   ```text
   winonly 311 on win-amd64: ✗ missing
     winonly-311-cp313-cp313-win_amd64.whl  built for cp313-cp313, not CPython 3.12
   ```

   There is no `via:` line saying which dependency requires `winonly`.

3. Run the same command with `--json`:

   ```bash
   uv run wheelgap tests/fixtures/uvproj -p 3.12 -t win-amd64 --explain --json --exit-zero
   ```

4. Inspect the JSON entry for `winonly`. Observed: it contains package information, results, and rejected wheels, but no `via` field.

**Reproduction Evidence**

- **Commit showing reproduction:** Pending. I have not pushed a reproduction commit to my fork yet, so there is no link to give.
- **Screenshots/logs:** The text output excerpt in step 2 above, and my local report `repro-draft.md` in the wheelgap working copy (not committed).
- **My findings:** The issue reproduces as described: the gap is reported, but no dependency path is shown in either text or JSON output. The cause is in `reachable()` in `src/wheelgap/check.py`, which returns only a set of reachable package indexes and keeps no parent information, so `Row`, `render_explain()`, and `report_json()` have no path to display. The traversal is depth-first, so even with parents recorded it would not guarantee the shortest path; a BFS that records each package's parent would.

  Limitations: the `uvproj` fixture only has one-step paths from `demo`, so this reproduction does not show multi-step or shortest-path behavior; I will need a new fixture for that. The fixture also models `devtool` as a dependency of `demo` instead of a separate dev-group root, so I have not seen how dev-group roots behave. I did not modify any project files during reproduction, and I have not posted a reproduction report on GitHub.

**Toolkit**

- Tool: Claude Code with the commands and notes from my local `CLAUDE.local.md`, used to run the baseline test suite and the two `--explain` commands, and to draft `repro-draft.md`.
- What it produced: A reproduction report with the environment versions, the baseline result (121 passed, 1 skipped), the exact commands, the observed output with no `via:` line and no `via` JSON field, and the root-cause finding in `reachable()`.
- Difficulties: uv was missing at first, so nothing ran until I installed it with Homebrew. The existing fixture is too shallow to show the interesting case (multi-step, shortest path), so the reproduction proves the missing feature but not the full expected behavior. It would also be easy to overstate the result, so I kept the expected output as an example rather than a claim about what the code does.
- What changed: I recorded the fixture limitations explicitly, planned to add a deeper fixture when I build the fix, and left the reproduction commit link pending instead of inventing one.

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
