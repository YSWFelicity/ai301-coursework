---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Decide whether exactly one candidate pull request is ready to submit.

A PR package contains a title, description, committed changes and diff, test evidence, and an implementation plan, including any documented deviations. Grade the package against its issue, its plan, its actual changes, its test results, and applicable repository standards.

Check whether the PR implements the plan faithfully, supports its claims with evidence, provides meaningful test results, and gives maintainers enough accurate information to review it.

Do not grade the contributor's skill, predict whether maintainers will merge the PR, or evaluate multiple packages in one run.

## Inputs and modes

Determine whether the request is in live mode or eval mode before gathering evidence.

### Live mode

Use live mode when checking the student's own draft PR before submission.

Read these inputs:

1. The issue URL supplied in the request. Read the issue description and relevant thread comments.
2. `plan.md`, including its diagnosis, scope, implementation approach, test plan, risks, and `## Deviations`.
3. The branch's committed diff against `main`, obtained from the repository working copy with `git diff main...HEAD`. Use the three-dot comparison and inspect the actual changed files and lines.
4. `pr_draft.md`, including the proposed PR title, description, checklist, testing claims, and reviewer notes.
5. `test_evidence.md`, including reproduction commands, before-and-after results, and repository check outputs.
6. The repository's `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and other explicitly applicable repository or issue-thread requirements.

Check the changed files against the plan and documented deviations. Check every material claim in the draft against the committed diff and actual test evidence.

For a house-chain student, use the supplied house issue, reproduction package, plan, implementation branch, and PR materials in place of the student's own artifacts. Apply the same rubric and verdict rule.

Do not treat uncommitted edits as part of the submitted diff. Do not assume that a claimed test passed without its actual output.

### Eval mode

Use eval mode when the input is a frozen practice PR package supplied by the evaluation harness.

Treat the provided package bundle as the complete and only source of facts.

Read its issue context, plan, deviations, PR title and description, diff, test evidence, and repository rules as supplied in the bundle.

Do not fetch GitHub pages, inspect the local repository, run commands, or supplement missing information with outside knowledge.

Grade every rubric check using the complete bundle. Apply the full verdict rule even when an early check fails.

## The scope seam (live mode only)

In live mode, read `scope.md` before any other input.

Verify that the requested PR belongs to the repository named on its `Repo:` line and follow the applicable house rules.

If the `Repo:` line still contains an unfilled placeholder, stop without grading. Tell the student to replace it with the correct section Path Review repository.

If the requested PR belongs to a different repository, refuse to grade it. State the configured scope and explain the mismatch.

Never guess the intended repository or silently expand the scope.

In eval mode, ignore `scope.md` entirely. The frozen bundle determines the evaluation context.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` before evaluating the outgoing PR text.

Compare both the proposed PR title and description with the guide's writing and communication rules.

Report any violated voice rule in the readable summary, identifying the relevant text and rule.

A voice-guide violation does not independently change the final verdict unless a check in `rubric.md` explicitly makes that requirement part of its pass condition.

In eval mode, ignore `voice-guide.md`. Apply only the universal communication requirements stated in the rubric and bundle.

## Component reads

Read and use the skill components from their fixed locations:

- `rubric.md` defines every check, its evidence, its pass condition, its weight, and the final verdict rule.
- `references/evidence-guide.md` identifies where each kind of PR evidence lives and what reliable evidence looks like.
- `procedure.md` defines the order and steps used to gather evidence, execute checks, and assemble the verdict.

Before grading, verify that `rubric.md` and `procedure.md` contain substantive instructions rather than only template comments or placeholders.

If either file has no substantive content, refuse to grade. Explain which component is incomplete. Do not invent replacement checks or operating steps.

Execute `procedure.md` as written. Use the evidence guide to locate supporting facts, then apply the exact pass conditions in `rubric.md`.

If the procedure omits a necessary step, report that gap rather than silently inventing a new workflow. Do not replace the rubric with personal judgment.

## Verdict and output

Return exactly one binary verdict:

- `accept`: the PR is ready to submit under the rubric's verdict rule.
- `reject`: the PR must be held because it does not satisfy the verdict rule.

Grade every rubric check as `pass`, `fail`, or `unclear`, and give one concise evidence line explaining each grade.

Use the exact check names defined in `rubric.md`.

A readable per-check summary may appear before the final JSON block.

End every completed grading response with the following fenced JSON block. Keep the schema, key order, and field names unchanged. Substitute the actual item, check names, grades, evidence, and verdict. Nothing may appear after the JSON block.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

1. **Evidence first.** Support every check grade with a specific fact, diff line, plan statement, repository requirement, or actual command output. Do not use vague statements such as "looks good" as evidence.

2. **Grade the artifact, not its polish.** A short but complete PR may be acceptable. A polished description cannot compensate for unsupported claims, missing tests, unrelated changes, or undisclosed deviations.

3. **Follow the rubric.** Apply each check's stated pass condition and weight consistently. If the rubric produces an unexpected result, report the issue for a future rubric revision rather than changing the grade based on intuition.

4. **Follow the procedure.** Execute the read order, evidence gathering, check execution, and verdict assembly steps as written in `procedure.md`. Report procedural gaps instead of improvising.

5. **Distinguish facts from claims.** A PR description saying that tests passed is not equivalent to a command and its printed output. A claimed file change must be visible in the diff. A claimed plan deviation must be recorded and explained.

6. **Do not invent missing evidence.** When a required fact cannot be verified, grade the relevant check `unclear`. If the rubric does not explicitly define another treatment, an unverifiable required claim fails the final readiness decision.

7. **Apply the verdict rule exactly.** Use the rule in `rubric.md` to combine the check grades into `accept` or `reject`. Do not introduce a third verdict, a numerical score, or an informal "accept with reservations" outcome.

8. **Grade the complete package.** In eval mode, always execute every check and return a complete verdict. Do not stop after the first failed check.

9. **Keep the result reviewable.** Identify the concrete reason for any rejection so the contributor can correct the PR before opening it.
