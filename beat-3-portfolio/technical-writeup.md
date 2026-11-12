# Tracing Dependency Problems: My Wheelgap Shortest-Path Contribution

<!-- Reference draft for Yingshu Wang to review, personalize, and rewrite before submission. -->

## Overview

For my CodePath AI301 contribution, I worked on [Wheelgap issue #26](https://github.com/wheelgap/wheelgap/issues/26). Wheelgap checks whether Python packages have compatible prebuilt distributions, called wheels, for selected Python versions and platforms. Its existing `--explain` output described why a wheel was incompatible, but did not explain which dependency introduced the affected package. That left developers with a compatibility report and another investigation to do.

I extended `--explain` for `uv.lock` inputs to show a shortest dependency path, including paths that differ across platforms. The implementation connects graph traversal, dependency-group information, text output, and JSON reporting. My contribution became [PR #45](https://github.com/wheelgap/wheelgap/pull/45), which the maintainer merged on November 8, 2026, Pacific time.

## What happened

I began by inspecting `check.py`, `cli.py`, and the existing fixtures. The original `reachable()` used a depth-first walk and returned only package indexes. It discarded the parent relationships needed to reconstruct a dependency chain. The maintainer's initial guidance confirmed that breadth-first search (BFS), with parents recorded at first discovery, would provide shortest paths.

I reproduced the missing information using this command from the Wheelgap repository root:

```bash
uv run wheelgap tests/fixtures/uvproj -p 3.12 -t win-amd64 --explain --exit-zero
```

My saved evidence records this block before the fix, at baseline `56c0974`:

```text
winonly 311 on win-amd64: ✗ missing
  winonly-311-cp313-cp313-win_amd64.whl  built for cp313-cp313, not CPython 3.12
```

The same command after the fix, at `7253546`, produced:

```text
winonly 311 on win-amd64: ✗ missing
  via: demo → winonly
  winonly-311-cp313-cp313-win_amd64.whl  built for cp313-cp313, not CPython 3.12
```

These excerpts come from sections 1–2 of my local `wheelgap/test_evidence.md`, collected November 8. The new line identifies the dependency responsible for introducing `winonly`; it does not resolve the compatibility problem itself. The committed `test_cli_explain_via_for_the_original_report` checks this behavior.

Following the maintainer's guidance, I replaced the stack with a `deque` and `popleft()`, recording each package's parent when first discovered. BFS explores successive dependency levels, so the first recorded route is a shortest one. `dependency_path()` reconstructs the names. Because platform markers can change which edges apply, I kept paths per target in `Row.via`. I also preserved dependency-group names in `Source.root_groups`, allowing paths to begin with labels such as `(dev group)`.

Before implementing JSON output, I [asked whether paths should be keyed by target](https://github.com/wheelgap/wheelgap/issues/26#issuecomment-6731727175). While waiting, I built the issue's flat-array form. The maintainer then replied:

> Good question, keyed by target please, like `rejected`:

This [feedback](https://github.com/wheelgap/wheelgap/issues/26#issuecomment-6731753911) addressed a real ambiguity: one package can arrive through different routes on different platforms. I revised `report_json()`, the JSON tests, README, CHANGELOG, and plan. The following field excerpt is recorded in my reproduction output with `--json` added to the command:

```json
"via": {"win-amd64": ["demo", "winonly"]}
```

The final structure includes gap targets with known paths and uses the same group labels as text output. It appears only with `--explain`; existing JSON keys remain unchanged.

I added 12 offline tests covering shortest paths, platform differences, dependency groups, text and JSON output, and inputs without graphs. An initial failure exposed a mistake in my test helper: it marked the wrong package editable. I corrected the helper. To test the shortest-path test itself, I temporarily replaced `popleft()` with `pop()` in an in-memory mutant. Section 4 of my saved evidence records:

```text
FAILED tests/test_check.py::test_via_is_a_shortest_path - AssertionError: assert {Target(major...g', 'x', 'c']} == {Target(major...'short', 'c']}
FAILED tests/test_check.py::test_via_differs_per_target - AssertionError: assert {Target(major...l', 'x', 'c']} == {Target(major...t', 'w', 'c']}
2 failed, 1 passed, 12 deselected
```

The truncation above is preserved from the saved output. The mutant chose a longer route; the real implementation passed both tests. This showed that the tests could detect the traversal regression.

Before opening the PR, I rebased onto upstream `e1432cb`, resolved conflicts, and added the newly required mypy check. Formatting, lint, and types passed; the recorded suite result was 161 passed, 1 skipped. The increase from 133 to 161 included upstream tests. The maintainer merged my nine-file contribution and [praised the question, regression test, and preserved group names](https://github.com/wheelgap/wheelgap/pull/45#issuecomment-6732093613).

## Limitations and what's next

Dependency paths require a usable `uv.lock` graph. Other input formats receive no text path and an empty JSON `via` object with `--explain`. The feature returns one shortest path per package and target, and does not preserve extras in names or label package-level dev-dependency edges.

A concrete follow-up would be to discuss preserving extras with the maintainer, then add a focused fixture and test before extending the edge representation. In my workflow, I would wait for clarification before building disputed output and recheck release headings before merge: the maintainer corrected my CHANGELOG placement afterward.

I used Claude Code to help trace code, write implementation and tests, and draft the PR description. I reviewed the changes, ran checks, and followed maintainer guidance; engineering responsibility remained mine. This portfolio text is a reference draft to rewrite in my own voice.
