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
**Pull Request:** https://github.com/patrickballard/wheelgap/pull/45

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

**Unit 7: Building a ship manifest and keeping it current through an upstream rebase**

I wrote a ship manifest (`ship-manifest.md`) listing the checks wheelgap's CONTRIBUTING.md requires before a PR, using `ruff format --check .` instead of `ruff format .` so the gate doesn't modify files. On my branch the format, lint, and test checks passed (133 passed, 1 skipped), but upstream had moved eight commits ahead, including a 0.3.0 release and a new mypy requirement in CONTRIBUTING.md and CI (#42), so the manifest was already out of date. I committed my change, rebased onto `upstream/main` (`e1432cb`), resolved small conflicts in `CHANGELOG.md` and `tests/test_explain.py`, added `uv run --group dev mypy` to the manifest as a required check, and re-ran everything: format, lint, and mypy are clean, and the suite gives 161 passed, 1 skipped. I then ran the Ship-Gate checker by hand on commit `7253546` against `upstream/main`. Before the run I removed a title line from the top of `pr_draft.md` so the file held only the PR body, and I passed `--base upstream/main` because my local `main` was still at the old baseline. The gate returned PASS: all four manifest checks were ok, and my pr-precheck accepted on all six rubric checks, noting only that my `plan.md` status line was stale. I fast-forwarded my local `main` to `upstream/main`, pushed the branch to my fork with a normal push, and ran `gh pr create` through Claude Code. The Ship-Gate hook honored the manual pass (same commit, base, title, body, manifest, plan, and evidence), my voice guard let the description through, and PR #45 opened.

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

- **Commit showing reproduction:** I did not push a separate reproduction commit. The reproduction is re-run as regression tests in the fix commit [`7253546`](https://github.com/patrickballard/wheelgap/pull/45/commits/72535462a973f7b2a6173cbf3fbe37e30c0f57d4) (for example `test_cli_explain_via_for_the_original_report`, which runs my original `-p 3.12 -t win-amd64` command).
- **Screenshots/logs:** The text output excerpt in step 2 above, and my local report `repro-draft.md` in the wheelgap working copy (not committed).
- **My findings:** The issue reproduces as described: the gap is reported, but no dependency path is shown in either text or JSON output. The cause is in `reachable()` in `src/wheelgap/check.py`, which returns only a set of reachable package indexes and keeps no parent information, so `Row`, `render_explain()`, and `report_json()` have no path to display. The traversal is depth-first, so even with parents recorded it would not guarantee the shortest path; a BFS that records each package's parent would.

  Limitations: the `uvproj` fixture only has one-step paths from `demo`, so this reproduction does not show multi-step or shortest-path behavior; I will need a new fixture for that. The fixture also models `devtool` as a dependency of `demo` instead of a separate dev-group root, so I have not seen how dev-group roots behave. I did not modify any project files during reproduction, and I have not posted a reproduction report on GitHub.

**Toolkit**

- Tool: Claude Code with the commands and notes from my local `CLAUDE.local.md`, used to run the baseline test suite and the two `--explain` commands, and to draft `repro-draft.md`.
- What it produced: A reproduction report with the environment versions, the baseline result (121 passed, 1 skipped), the exact commands, the observed output with no `via:` line and no `via` JSON field, and the root-cause finding in `reachable()`.
- Difficulties: uv was missing at first, so nothing ran until I installed it with Homebrew. The existing fixture is too shallow to show the interesting case (multi-step, shortest path), so the reproduction proves the missing feature but not the full expected behavior. It would also be easy to overstate the result, so I kept the expected output as an example rather than a claim about what the code does.
- What changed: I recorded the fixture limitations explicitly, planned to add a deeper fixture when I build the fix, and left the reproduction commit link pending instead of inventing one (I later pointed it at the regression tests in the fix commit `7253546`, which re-run my original command).

---

## Solution Approach

**Analysis**

The missing `via:` path comes from how the graph walk works and from what `Row` can hold:

- `reachable(source, target)` in `src/wheelgap/check.py:176` walks the uv.lock graph with a stack (`stack.pop()`), so the walk is depth-first. It returns `set[int]`, the indexes of the packages installed on that target, and keeps no record of how it reached each one. Even if it recorded parents, a depth-first walk can reach a package along a longer path first, so the recorded path would not always be the shortest.
- `check()` (`check.py:211`) calls `reachable()` once per target and only asks `i in installed[t]`, so the path information is lost there. `Row` (`check.py:30`) holds only `package` and `results: dict[Target, Status]`, so there is nowhere to store a path.
- `render_explain()` (`src/wheelgap/cli.py:237`) and `report_json()` (`cli.py:258`) can only show what `Row` carries, so neither can print a path.
- `parse_uv_lock()` (`src/wheelgap/sources.py:95-97`) adds `[manifest].dependency-groups` entries to `source.root_edges` but drops the group name. A package reached that way has no parent package, so without the group name there is nothing to put at the start of its path. This answers the open question about dev-group roots from my reproduction.
- Only uv.lock inputs have a graph (`source.edges is not None`). Every other input uses `applies()` with package markers, so there is no path to show for them.

**Proposed Solution**

Following the issue and the maintainer's comment:

1. **BFS shortest paths.** Replace the stack in `reachable()` with a `collections.deque` and `popleft()`, seeded with `roots` and then the `root_edges` targets (a multi-source BFS). Each package's parent is recorded when it is first added to `seen`, so the first path found is a shortest one.
2. **Per-target parents.** Markers can send the walk down different branches on different targets, so `reachable()` returns `dict[int, int | str | None]` (package index → parent) and still runs once per target. A dict keeps the existing `i in installed[t]` check working. `Row` gets `via: dict[Target, list[str]]`, filled only for targets where the package is needed and only when the source has a graph.
3. **Dependency group roots.** `Source` gets `root_groups: dict[int, str]` (root edge index → group name), filled by `parse_uv_lock()`. A path that starts at a group root begins with a label, following the maintainer's example: `via: (dev group) → pytest`. Because the field has a default, code and tests that build a `Source` directly don't change.
4. **Text output.** `render_explain()` prints `  via: demo → fastlib → nativeext` under each target's header, before the rejected wheels, only when the path has at least two entries.
5. **JSON output.** The issue's example is a flat array, but a flat array would have to pick one target's path and misreport the others when targets disagree. Putting the path inside `rejected[label]` would turn a list into an object and break existing consumers, and switching between an array and an object depending on the input is worse for consumers than either fixed shape. I chose a new sibling key, `"via": {label: [names]}`, built exactly like `rejected`: it appears only with `--explain`, covers the same gap targets, and each value is the issue's flat array unchanged. Non-uv inputs get `"via": {}`. Unlike the text line, JSON includes a one-entry path when it is known, so a consumer can tell a direct requirement apart from "no graph". I will point this choice out in the PR description so the maintainer can ask for the flat form instead.

What I am deliberately **not** changing:

- The `Edge` tuple and `_uv_edges()`. A project's own `[package.dev-dependencies]` stays merged into its edges, so the fixture's `devtool` shows as `demo → devtool`. Labelling those would mean changing `Edge`, which other tests depend on. Only manifest-level group roots get a `(group)` label.
- Extras in names. `_uv_edges()` doesn't keep extras, so the path shows plain names (`uvicorn`, not `uvicorn[standard]`).
- Paths for non-uv inputs (pylock, poetry, pdm, Pipfile, requirements). There is no graph, so the line is left out.
- The table output, exit codes, and the `--explain` flag itself. No new flag is added.
- All-paths output. Only one shortest path is shown per package and target.

**Implementation Plan**

1. `src/wheelgap/sources.py`: add `root_groups: dict[int, str]` to `Source`, and record each group name in `parse_uv_lock()`.
2. `src/wheelgap/check.py`, `reachable()`: switch to BFS with `deque`, record the parent at first discovery (`int` for a package, `str` for a group root edge, `None` for a root or requirement), return `dict[int, int | str | None]`, and update the docstring.
3. `src/wheelgap/check.py`: add a `_path(source, parents, i) -> list[str]` helper and `via: dict[Target, list[str]]` on `Row`; in `check()`, set `row.via[t]` when the source has a graph and the package is needed on `t`.
4. `src/wheelgap/cli.py`: print the `via:` line in `render_explain()` when the path has two or more entries, and add the `via` object in `report_json()` under `--explain`.
5. `tests/fixtures/uvproj/uv.lock`: as the maintainer suggested, add one transitive package. `fastlib` depends on a new sdist-only `nativeext 0.3`, giving the path `demo → fastlib → nativeext`. I checked the other tests that use `uvproj` (`test_cli.py`, `test_dockerfile.py`, `test_sources.py`), and the new package doesn't break them.
6. Tests, all offline with fixtures or in-code `Source` objects:
   - `tests/test_check.py`: `test_reachable_shortest_path` (a graph where the old stack walk would record a longer path), `test_via_differs_per_target` (a Windows-only branch), `test_via_from_dependency_group`, and extensions to `test_uv_lock_graph` and `test_markers_on_packages`.
   - `tests/test_sources.py`: a small uv.lock in `tmp_path` with a `dev` dependency group, asserting `root_groups == {0: "dev"}`.
   - `tests/test_explain.py`: the `via:` text line for both `nativeext` and my original reproduction (`via: demo → winonly`), no `via:` for a non-uv input, the JSON `via` object matching the `rejected` keys, and no `via` key without `--explain`.
7. `README.md`: add a `via:` line and one sentence to the `--explain` example. `CHANGELOG.md`: add an entry under `## Unreleased`, leaving the credit line to the maintainer as recent history shows.
8. Before opening the PR: run `ruff format`, `ruff check`, and `pytest`, then rerun both reproduction commands and compare against `repro-draft.md`. The PR description will include `Closes #26` and the JSON-shape rationale.

**Deviations** (recorded in `plan.md` as they happened):

1. **JSON `via`: a detour to a flat array, then back to keyed by target on the maintainer's answer.** Plan Check accepted the plan above but flagged that the keyed object went against the issue's flat-array example, so I asked on the thread before building the JSON ([my question](https://github.com/patrickballard/wheelgap/issues/26#issuecomment-6731727175)). While waiting, I built the issue's flat array, using the first gap target's path when targets disagreed and leaving the key out when no path was known. The maintainer then answered "keyed by target please, like `rejected`", because a flat array would hide a package that comes in by different routes on different platforms. They also asked for the same targets as `rejected` (gap targets only, leaving out a target with no path) and the same `(dev group)` label as the text line. Plan Check rejected the flat-array version of the plan on the conventions check, so I switched `report_json()` back to the keyed form and updated its tests, the README, the CHANGELOG and `plan.md`. The BFS, `Row.via` and the text output were already per target, so they did not change. The re-graded plan was accepted.
2. **A JSON test for dependency-group labels.** The maintainer's request that JSON paths start with the same `(dev group)` label needed no code change, because one helper builds the path for both outputs. I added `test_json_explain_via_from_dependency_group` to pin it.
3. **Test names and layout.** The shortest-path test is `test_via_is_a_shortest_path` rather than `test_reachable_shortest_path`. The uvproj path assertions went into a new `test_uv_lock_via` instead of extending `test_uv_lock_graph`, so status and path failures are reported separately. I added `test_via_from_plain_requirement` for packages reached from a top-level requirement, and the group-names test in `test_sources.py` uses two groups plus a plain requirement (`root_groups == {0: "dev", 1: "docs"}`).
4. **Helper renamed** from `_path()` to `dependency_path()`, a clearer name next to `reachable()`. No behaviour difference.

**Toolkit**

- Tool: Claude Code, used to trace the code path from `parse_uv_lock()` through `reachable()` and `check()` to `render_explain()` and `report_json()`, and to draft a written plan (`plan.md`, kept local and excluded from Git) from the issue, the maintainer's comment, and my reproduction findings.
- What it produced: A plan with a line-referenced root-cause analysis, a five-part solution (BFS, per-target parents, group-root labels, text line, JSON key), an explicit "not changing" list, a twelve-step implementation and test plan in `plan.md` (condensed to eight steps above), and a comparison of four JSON shapes with the reason for the one I chose.
- Difficulties: The issue's own JSON example (a flat array) conflicts with the maintainer's point that paths differ per target, so the issue could not simply be followed literally. Tracing also turned up two gaps the reproduction had not shown: `parse_uv_lock()` drops dependency-group names, and `_uv_edges()` drops extras, so the issue's `uvicorn[standard]` example cannot be reproduced exactly without wider changes.
- What changed: I chose the additive `via` object keyed by target and planned to flag it in the PR so the maintainer can overrule it. I added `root_groups` to cover the group-root question, left extras and `Edge` out of scope on purpose, and checked every existing test that uses the `uvproj` fixture before planning to extend it.

---

## Testing Strategy

The fix is proven by three kinds of observable checks, all run locally on my branch
(`issue-26-explain-dependencies`). I first verified the change uncommitted on my baseline
`56c0974`, then committed it as `7253546`, rebased onto the latest `upstream/main` (`e1432cb`), and
re-ran everything. The full commands and output
are in `test_evidence.md` in the wheelgap working copy (excluded from Git, like my other notes).

**1. The original reproduction, before and after**

I ran my two reproduction commands against an untouched copy of the baseline (extracted with
`git archive 56c0974`) and against my branch:

```bash
uv run wheelgap tests/fixtures/uvproj -p 3.12 -t win-amd64 --explain --exit-zero
uv run wheelgap tests/fixtures/uvproj -p 3.12 -t win-amd64 --explain --json --exit-zero
```

- Before: `winonly 311 on win-amd64: ✗ missing` followed directly by the rejected wheel, with no
  `via:` line, and no package in the JSON had a `via` key.
- After: the same block now has `via: demo → winonly` under the header, and the JSON entry has
  `"via": {"win-amd64": ["demo", "winonly"]}`. The new two-step fixture package shows
  `via: demo → fastlib → nativeext`.
- A non-uv input (`pylockproj -t macos-arm64 --explain`) still prints no `via:` line, because it
  has no dependency graph.

**2. Focused regression tests**

- **Shortest path:** `test_via_is_a_shortest_path` builds `root → [short, long]`, `short → c`,
  `long → x → c` and expects `["root", "short", "c"]`. To check that it really guards the BFS
  change, I ran it against an in-memory mutant of `reachable()` with `popleft()` swapped for
  `pop()` (a depth-first walk). The mutant fails `test_via_is_a_shortest_path` and
  `test_via_differs_per_target` because it returns the longer path through `x`; the real code
  passes both.
- **Per target and groups:** `test_via_differs_per_target` (a Windows-only branch gives a
  different path on `win-amd64`), `test_via_from_dependency_group` (`["(dev group)", "tool"]`),
  `test_via_from_plain_requirement`, `test_uv_lock_via`, and `test_uv_lock_dependency_group_names`
  in `test_sources.py`.
- **JSON shape:** the maintainer answered my question on the issue with "keyed by target please,
  like `rejected`". `test_json_explain_via` checks that `via` keys equal `rejected` keys for
  every package (with two targets, `winonly` only gets `win-amd64`),
  `test_json_explain_via_from_dependency_group` checks that JSON uses the same `(dev group)` label
  as the text line, and `test_json_explain_no_via_without_a_graph` checks `"via": {}` for pylock.

All tests are offline and use local fixtures or a small uv.lock written to `tmp_path`, as
CONTRIBUTING.md requires.

**3. Ship-manifest checks**

Post-rebase (current), on commit `7253546` over `upstream/main` `e1432cb`:

| Check | Command | Result |
|---|---|---|
| Formatting | `uv run --group dev ruff format --check .` | `28 files already formatted` (exit 0) |
| Lint | `uv run --group dev ruff check .` | `All checks passed!` (exit 0) |
| Types | `uv run --group dev mypy` | `Success: no issues found in 11 source files` (exit 0) |
| Tests | `uv run --group dev pytest` | **161 passed, 1 skipped** (exit 0) |

Both original reproduction commands still give `via: demo → winonly` and
`"via": {"win-amd64": ["demo", "winonly"]}` on the rebased commit.

Pre-rebase (superseded), uncommitted on `56c0974`: formatting clean (22 files), lint clean, and
**133 passed, 1 skipped**, up from 121 passed, 1 skipped on the untouched baseline. mypy was not a
project check yet. Upstream added it to CONTRIBUTING.md and CI in #42 while I was working, so I added
it to my ship manifest. The jump from 133 to 161 comes from upstream's new tests (#38–#44), not
from my change.

After the JSON change and the `plan.md` update, I re-ran Plan Check on the plan and draft comment,
and it returned **accept** on all five required checks (diagnosis, scope, approach, test-plan,
conventions).

**Toolkit**

- Tool: Claude Code to write the tests, run the reproduction against both the baseline and the branch, and run the mutation check; my ship manifest (`ship-manifest.md`) for the must-pass checks (now four, with mypy); and Plan Check to re-grade the plan after each deviation.
- What it produced: `test_evidence.md`, with before/after output for both reproduction commands, the BFS regression test and its mutation result, the target-keyed JSON output, and the ship-manifest results before the rebase (format clean, lint clean, 133 passed, 1 skipped) and after it (format clean, lint clean, mypy clean, 161 passed, 1 skipped). Plan Check's final verdict was accept on all five checks.
- Difficulties: While waiting for the maintainer, I first built the issue's flat JSON array. The maintainer then asked for target-keyed JSON, and Plan Check rejected the updated plan on the conventions check until it followed that direction. Smaller problems: one of my new tests failed at first because its helper marked the wrong package as editable (a bug in the test, not the code), and ruff flagged a test line over 120 characters. A passing test also doesn't prove it would catch the bug, so I needed a separate check for that. Upstream also moved ahead while I worked: my diff no longer applied cleanly, and mypy became a required check.
- What changed: I switched `report_json()` to the keyed form, updated the JSON tests, README, CHANGELOG and `plan.md` Deviations, and re-ran Plan Check until it accepted. I fixed the test helper and shortened the line. I added the depth-first mutant run to show the shortest-path test fails without BFS, and used `ruff format --check` from the manifest so the formatting check doesn't change files. I rebased onto `upstream/main`, resolved the conflicts, added mypy to the manifest, and re-ran every check and both reproduction commands on the rebased commit.

---

## Implementation Notes

All dates are local time. The full command output is in `test_evidence.md` in my wheelgap working copy.

**2026-11-08: JSON question and first build**

Before building the JSON output, I asked the maintainer on the issue whether `via` should be keyed by target or stay a flat array ([my question](https://github.com/patrickballard/wheelgap/issues/26#issuecomment-6731727175)). While waiting, I built the BFS change in `reachable()`, per-target `Row.via`, `root_groups` for dependency groups, the text `via:` line, the new `nativeext` fixture package, and the tests, using the issue's flat JSON array. One new test failed at first because my test helper marked the wrong package as editable; the bug was in the test, not the code. All tests passed after I fixed it (133 passed, 1 skipped), and I checked that the shortest-path test fails if the BFS is swapped back to a stack.

**2026-11-08: Switching to keyed JSON**

The maintainer answered "keyed by target please, like `rejected`", with two details: use the same targets as `rejected`, and the same `(dev group)` label as the text line. Plan Check rejected my updated plan on the conventions check because it still described the flat array. I changed only `report_json()`, its JSON tests, the README and the CHANGELOG, then recorded the change in `plan.md`. Ruff flagged a test line over 120 characters, which I shortened. Plan Check then accepted on all five checks.

**2026-11-08: Rebase, Ship-Gate, and PR**

While I prepared the PR description, upstream moved 8 commits ahead (a 0.3.0 release, Pyodide support, `--list-targets`, a Python API, and a new mypy requirement), and my diff no longer applied cleanly. I committed only the nine implementation files, rebased onto `upstream/main` (`e1432cb`), and resolved conflicts in `CHANGELOG.md` (my entry at the top of `## Unreleased`, not in 0.3.0) and `tests/test_explain.py` (kept upstream's Pyodide test and mine). `sources.py` merged automatically. I added mypy to my ship manifest. After the rebase, format, lint and mypy were clean and the suite gave 161 passed, 1 skipped. The Ship-Gate passed, and I opened PR #45.

**Toolkit**

- Tool: Claude Code, used to implement the change and tests, run the reproduction and checks, and do the rebase; plus Plan Check, my ship manifest, and the Ship-Gate.
- What it produced: The fix commit `7253546` (9 files), `test_evidence.md` with before/after output, and an updated `plan.md` with a Deviations section.
- Difficulties: The JSON shape changed after I had already built it. Upstream moved and added mypy while I was finishing, which caused rebase conflicts. A bug in my own test helper made the first test run fail.
- What changed: I followed the maintainer's answer and recorded the detour as a deviation. I rebased and resolved the conflicts by keeping both sides, added mypy to the manifest, and re-ran every check on the rebased commit before opening the PR.

---

## Pull Request

**PR Link:** https://github.com/patrickballard/wheelgap/pull/45

**PR Title:** `--explain: show the dependency path to each gap package (uv.lock)`

**PR Description:** The description follows wheelgap's PR template and includes `Closes #26`. It explains that, for `uv.lock` inputs, `--explain` now shows the shortest dependency path to each gap package (for example `via: demo → winonly`). It covers the breadth-first walk in `reachable()`, per-target paths in `Row.via`, `(dev group)` labels from `Source.root_groups`, the target-keyed JSON `via` object "as discussed on the issue", the fixture change, and the README and CHANGELOG updates. It also lists what is not changed (extras, package-level dev-dependency labels, non-uv inputs, only one shortest path) and the tests. Finally, it records the local results on the rebased branch (ruff format and lint, mypy, and 161 passed, 1 skipped) and gives an AI-assistance disclosure.

**Tests:** 12 new offline tests in `test_check.py`, `test_sources.py` and `test_explain.py`. They cover the shortest path (which fails if the BFS is swapped back to a stack), per-target paths, dependency-group labels in text and JSON, JSON `via` keys matching `rejected`, no path for non-uv inputs, and my original `winonly` reproduction. On the PR, all 9 upstream CI checks passed (lint, the test matrix on Linux, macOS and Windows across Python 3.11–3.14t, and the uv.lock self-check).

**Maintainer Feedback:**

- 2026-11-08 (on the issue, before the PR): the maintainer asked for `via` keyed by target like `rejected`, with gap targets only and the same `(dev group)` label as the text output. I changed the JSON to match before opening the PR (see Deviations).
- 2026-11-08 (on the PR, at merge): the maintainer merged the PR and wrote: "This was excellent: asking about the JSON shape before writing it, the test that fails if the walk goes back to depth-first, and keeping the dependency-group names that `parse_uv_lock` used to throw away. A really thorough first PR, and thanks for the AI-assistance note too." They also explained a follow-up fix: my branch predated the 0.4.0 release, so my CHANGELOG line landed under `## 0.4.0`. In commit [`f71205a`](https://github.com/patrickballard/wheelgap/commit/f71205a) ("Changelog: #45 is unreleased; credit it"), they moved it to a new `## Unreleased` section and changed the reference to `(#45, thanks @YSWFelicity)`, so it will go out in the next release. I replied to thank them. No code changes were requested.

**Status:** Merged on 2026-11-08 (20:15 local, 2026-11-09 04:15 UTC) by the maintainer, as merge commit `1d06514`, with the CHANGELOG follow-up in `f71205a`. Issue #26 is closed by the PR's `Closes #26` line.

**Toolkit**

- Tool: The Ship-Gate (`check_gate.py`), run by hand and then through its Claude Code hook on `gh pr create`, with my ship manifest and pr-precheck; my voice guard on the PR description.
- What it produced: A manual PASS on commit `7253546`: all four manifest checks were ok, and pr-precheck accepted on all six rubric checks. On `gh pr create`, the hook honored that pass and PR #45 opened.
- Difficulties: The gate's default base was my stale local `main`, which would have counted upstream's commits as mine. `pr_draft.md` also began with a title line that would have ended up in the PR body. pr-precheck noted that my `plan.md` status line was out of date, though it didn't change the verdict.
- What changed: I ran the gate with `--base upstream/main`, then fast-forwarded local `main` so the hook's default base matched. I trimmed `pr_draft.md` to the body only. I left `plan.md` unchanged so the pass stayed valid. One thing the gate couldn't catch: a release was cut between my rebase and the merge, so my CHANGELOG line ended up under the wrong version. The maintainer fixed that in `f71205a`. Next time, I'd check whether `## Unreleased` has moved right before the merge, or point it out in the PR.

---

## Learnings and Reflections

**Technical Skills Gained**

I learned how a small change in traversal order changes what a graph walk can promise. Switching `reachable()` from a stack to a `deque` with `popleft()`, and recording each package's parent at the moment it is first seen, turns "everything reachable" into "a shortest path to each package". Because markers like `sys_platform == 'win32'` send the walk down different branches on different targets, I also learned to keep paths per target rather than one per package. On the output side, I learned to design an additive JSON field: a new `via` key built like the existing `rejected`, so no existing key changes for current consumers. Finally, I learned to mutation-test my own test, by swapping `popleft()` back to `pop()` and confirming that `test_via_is_a_shortest_path` fails, so I know it actually guards the BFS change.

**Challenges Overcome**

The hardest moment was the JSON shape. The issue showed a flat array, but paths can differ per target. I asked the maintainer, built the flat version while waiting, and then switched to the keyed form when they answered, recording the detour as a deviation after Plan Check rejected the out-of-date plan. The second challenge was upstream moving eight commits ahead while I was finishing: my diff no longer applied, and mypy had become a required check. I committed only my nine files, rebased onto `upstream/main`, kept both sides in the `CHANGELOG.md` and `tests/test_explain.py` conflicts, added mypy to my ship manifest, and re-ran every check (161 passed, 1 skipped). Before the Ship-Gate run, I also caught that my stale local `main` would have counted upstream's commits as mine, and that `pr_draft.md` began with a title line that would have ended up in the PR body.

**What I'd Do Differently Next Time**

I would wait for the maintainer's answer before building the part of a feature that is still under discussion, instead of building a version I might throw away. I would fetch upstream at the start of implementation and again right before opening the PR, so a rebase and a new required check like mypy don't arrive at the last step. I would also check where my CHANGELOG line sits just before the merge: a release was cut between my rebase and the merge, and the maintainer had to move my entry in `f71205a`. On the positive side, I would keep asking a focused question early, since the maintainer specifically praised asking about the JSON shape before writing it.

---

## Resources Used

- [wheelgap CONTRIBUTING.md](https://github.com/patrickballard/wheelgap/blob/main/CONTRIBUTING.md): the setup commands, the rules for offline tests, a CHANGELOG line under `## Unreleased`, README updates and `Closes #`, the AI-assistance policy, and later the new `uv run --group dev mypy` requirement.
- [Issue #26 and its discussion](https://github.com/patrickballard/wheelgap/issues/26): the original proposal, including the shortest-path hint and the `via:` example.
- [The maintainer's pointers comment](https://github.com/patrickballard/wheelgap/issues/26#issuecomment-6731171071): the `collections.deque`/`popleft()` BFS with `parent[j] = i` at first discovery, keeping paths per target, the `via: (dev group) → pytest` label, leaving the line out for non-uv inputs, and adding one transitive package to the `uvproj` fixture.
- [The maintainer's JSON clarification](https://github.com/patrickballard/wheelgap/issues/26#issuecomment-6731753911), answering [my question](https://github.com/patrickballard/wheelgap/issues/26#issuecomment-6731727175): `via` keyed by target like `rejected`, gap targets only, and the same `(dev group)` label as the text output.
- [wheelgap's PR template](https://github.com/patrickballard/wheelgap/blob/main/.github/pull_request_template.md): the structure and checklist I followed for the PR #45 description.
- The maintainer, [@patrickballard](https://github.com/patrickballard), who welcomed my claim with code pointers, answered my JSON question on the issue, reviewed and merged [PR #45](https://github.com/patrickballard/wheelgap/pull/45), and explained the CHANGELOG fix in the [merge comment](https://github.com/patrickballard/wheelgap/pull/45#issuecomment-6732093613).
