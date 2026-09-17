# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73

**Verdict output**

Summary

Grading #73 — "README and .env.example disagree about which LLM API key to set" (live mode, capture date 2026-09-17).

- maintainer-active — pass: non-bot commit by Aburke225 on 2026-09-16, within 90 days.
- repo-in-use — pass: isArchived=false; last push 2026-09-16, within 180 days.
- newcomer-scope — pass: bounded two-file docs reconciliation ("Make the two files agree"); labels good first issue/docs/tier-1; not an umbrella/design/usage issue.
- unclaimed — pass: no assignees, no linked or mentioned PRs, empty comment thread.
- ai-policy — pass: docs/CONTRIBUTING.md states no AI ban; commits are even Co-Authored-By: Claude.

All five required checks pass → accept.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73",
  "checks": [
    {
      "name": "maintainer-active",
      "grade": "pass",
      "evidence": "Non-bot commit by Aburke225 on 2026-09-16, within 90 days of capture date 2026-09-17"
    },
    {
      "name": "repo-in-use",
      "grade": "pass",
      "evidence": "isArchived=false; pushedAt 2026-09-16, within 180 days"
    },
    {
      "name": "newcomer-scope",
      "grade": "pass",
      "evidence": "Bounded docs fix: 'Make the two files agree'; labels good first issue/docs/tier-1"
    },
    {
      "name": "unclaimed",
      "grade": "pass",
      "evidence": "assignees=[], no linked/mentioned PRs, comment thread empty"
    },
    {
      "name": "ai-policy",
      "grade": "pass",
      "evidence": "docs/CONTRIBUTING.md states no AI ban; commits are Co-Authored-By Claude"
    }
  ],
  "verdict": "accept"
}
```

---

## Eval iterations

**Run history**

Run 1:
`agreement: 18/20 scored items  (bar: 18/20: PASS)`

Targeted re-run:
`agreement: 1/2 scored items`

Final run:
`agreement: 20/20 scored items  (bar: 18/20: PASS)`

**Issue analysis**

`issue-19`

My rubric initially decided `reject`, while the gold label was `accept`.

The run reported:
`failed: newcomer-scope`

The issue described one UI-freeze bug but listed two possible causes and several implementation suggestions. My first version of `newcomer-scope` required "one bounded contribution," so the grader treated the multiple causes and suggestions as evidence that the issue was too broad.

**Check rationale**

Current check:

`| newcomer-scope | Issue body and Comments section, including the issue's age and history of prior attempts | Pass if the issue asks for a bounded contribution and is not an umbrella/tracking issue, unresolved design debate, pure usage question, or work that a maintainer says requires changes to core internals. Multiple possible causes or implementation suggestions for one bounded problem do not fail this check. Fail if the issue has been open for years with several abandoned contribution attempts indicating hidden difficulty. | required |`

I changed this check because two cases exposed weaknesses in the earlier wording. Issue-19 showed that one bounded bug can still have multiple possible causes or implementation ideas. Issue-15 showed that an old issue with repeated abandoned attempts can look simple on the surface while actually being difficult for a newcomer. The current form distinguishes those two situations.

**Trade-offs**

The revised check changed the results of the issues I re-ran with `--only`. Issue-15 changed from an incorrect `accept` to `reject` because it had years of abandoned contribution attempts. Issue-19 could pass because its multiple causes and suggestions still described one bounded bug. After the revision, the final full run reached `20/20`.

---

## Selection rationale

**Selection rationale**

1. This issue fits my available time because it is a small documentation task with an estimated effort of 1–2 hours. It only involves `README.md` and `.env.example`, so the scope is limited and easy to understand.

2. The verdict correctly identified that the repository is active, the issue is unclaimed, the work is bounded, and the contribution policy does not block AI-assisted work. The rubric could not judge my own comfort with the task or how quickly I could understand the project setup. I also considered that this issue only requires fixing documentation instead of changing core application behavior.

3. I expect the claiming difficulty to be low because the issue currently has no assignee, no linked or mentioned PR, and no comments showing that someone else is working on it. I will still check the issue again before claiming it in Unit 2.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
