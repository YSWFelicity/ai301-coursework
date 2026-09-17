# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check             | Evidence                                                                                                                          | Pass condition                                                                                                                                                                                                                                                                                                                                                                                                                         | Weight   |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| maintainer-active | Repo facts: last 5 default-branch commits and maintainer first-response sample; Comments: Owner, Member, or Collaborator activity | Pass if at least one non-bot default-branch commit occurred within 90 days of the capture date, or an Owner, Member, or Collaborator responded to a recent issue within 30 days                                                                                                                                                                                                                                                        | required |
| repo-in-use       | Repo facts: archived status, latest release, and last push to any branch                                                          | Pass if the repository is not archived and has either a release or a push within 180 days of the capture date                                                                                                                                                                                                                                                                                                                          | required |
| newcomer-scope    | Issue body and Comments section, including the issue's age and history of prior attempts                                          | Pass if the issue asks for a bounded contribution and is not an umbrella/tracking issue, unresolved design debate, pure usage question, or work that a maintainer says requires changes to core internals. Multiple possible causes or implementation suggestions for one bounded problem do not fail this check. Fail if the issue has been open for years with several abandoned contribution attempts indicating hidden difficulty. | required |
| unclaimed         | Repo facts: assignees and linked PRs; Comments section: claim comments and mentioned PRs                                          | Pass if there is no current assignee, no open linked or mentioned PR, and no unresolved recent comment indicating that another contributor is actively working on the issue                                                                                                                                                                                                                                                            | required |
| ai-policy         | Repo facts: contribution policy; CONTRIBUTING.md, dedicated AI policy files, and contribution templates when provided             | Pass unless the contribution policy explicitly bans AI-generated or AI-assisted contributions; disclosure, testing, human-review requirements, or no stated AI policy pass                                                                                                                                                                                                                                                             | required |

## Verdict rule

Accept only if every required check passes. An unclear grade on a required check counts as a fail. Preferred checks do not change the verdict and may only be used to rank accepted issues.
