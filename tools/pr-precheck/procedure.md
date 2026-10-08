# Procedure: how this tool grades a PR package

## Read order

1. Identify the grading mode before reading the PR package.
   - In live mode, read `scope.md` first. Confirm that its `Repo:` line is filled and that the requested PR belongs to the scoped repository. Refuse grading if the scope is missing or mismatched.
   - In eval mode, use only the supplied package bundle. Do not read `scope.md`, access GitHub, inspect local files, or run commands.

2. Read `rubric.md` and `references/evidence-guide.md`. Identify the exact check names, required or preferred weights, pass conditions, evidence locations, and verdict rule.

3. Confirm that `rubric.md` and `procedure.md` contain substantive instructions. Refuse grading if either is empty or only contains template comments.

4. Read the issue context first. Record the reported problem, expected outcome, relevant constraints, and any explicit requirements from the issue discussion.

5. Read the implementation plan before the diff. Record:
   - The planned scope and intended files.
   - The proposed implementation steps.
   - The test plan and expected results.
   - Any documented deviations, limitations, or trade-offs.

6. Read the committed diff against the plan. In live mode, run `git diff main...HEAD` from the Path Review working copy. Record every changed file and the meaningful changes in each hunk. In eval mode, use only the diff supplied in the package.

7. Read the test evidence. Record the actual commands, outputs, results, and any missing or explicitly skipped checks.

8. Read the PR title and description. Identify claims about implemented changes, plan fidelity, tests, limitations, issue linkage, and reviewer notes.

9. Read the applicable repository standards. In live mode, inspect `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, relevant issue instructions, and `voice-guide.md`. In eval mode, use only repository standards included in the package and ignore the voice guide.

10. Finish reading all available package components before assigning any check grades. This prevents an attractive description from influencing the interpretation of the actual diff or evidence.

## Evidence gathering

1. Build a plan-to-diff comparison:
   - List the files and behavior the plan says should change.
   - List the files and behavior actually changed in the committed diff.
   - Identify planned work that is absent and actual work that is outside the plan.
   - Check whether material differences are documented under `## Deviations`.
   - Record any PR-description claim of exact plan fidelity that conflicts with the diff.

2. Build a test-plan-to-evidence comparison:
   - List the checks and behavior the plan says should be tested.
   - Locate the corresponding commands and observable results in the test evidence.
   - Compare the results with the expected behavior.
   - Identify required repository checks and whether their execution is evidenced.
   - Separate actual terminal output from unsupported statements such as "all tests passed."
   - Record disclosed limitations, skipped checks, and their stated reasons.
   - Do not invent results or treat missing output as proof of success.

3. Build a diff-reviewability record:
   - List every changed file and relevant hunk.
   - Compare each change with the issue's purpose and the plan's scope.
   - Identify unrelated refactors, generated artifacts, temporary files, or unnecessary edits.
   - Determine whether any unrelated work materially obscures the intended fix.
   - Distinguish necessary supporting changes from unrelated changes.

4. Build a description-to-evidence comparison:
   - Extract the PR's material claims about changes, testing, and plan fidelity.
   - Match each claim to the actual diff, plan, or test evidence.
   - Identify false claims, unsupported success statements, and important omissions.
   - Record whether deviations, risks, and limitations are accurately disclosed.

5. Build a repository-standards record:
   - Extract applicable requirements from the repository facts, PR template, contribution guidelines, and issue instructions.
   - Check the PR title, issue linkage, description, testing disclosures, reviewer notes, and required AI-use disclosure.
   - Distinguish substantive missing information from harmless formatting differences.
   - In live mode, separately record any `voice-guide.md` violations for the readable summary.

6. Use `references/evidence-guide.md` to resolve where each evidence family lives. If an expected artifact is absent, record its absence explicitly rather than searching outside the allowed evidence sources.

## Check execution

Execute every check in the order listed in `rubric.md`.

1. **Plan fidelity**
   - Compare the committed diff with the plan's intended scope and documented deviations.
   - Pass when the material implementation is consistent with the plan or honestly documented deviations.
   - Fail when material drift is undisclosed or the description falsely claims exact fidelity.
   - Mark unclear when the available plan or diff is insufficient to verify fidelity.

2. **Test evidence**
   - Compare the plan's test requirements with actual commands and observable results.
   - Verify that material testing claims are supported.
   - Consider accurately disclosed and justified limitations under the rubric's honest-outcome rule.
   - Fail unsupported success claims, fabricated results, or missing essential verification.
   - Mark unclear when essential results cannot be established from the package.

3. **Diff reviewability**
   - Examine the changed-file list and individual hunks against the issue and plan.
   - Pass when the change is focused and understandable.
   - Fail when unrelated changes or debris materially obscure the intended fix.
   - Mark unclear when the diff is too incomplete to assess.

4. **Repository standards**
   - Compare the PR title and description with applicable repository requirements.
   - Pass when substantive requirements are met, even if equivalent wording differs from the template.
   - Fail when required issue linkage, disclosure, or other substantive information is missing.
   - Mark unclear when a stated requirement cannot be verified.

5. **Description accuracy**
   - Compare the PR description with the issue, plan, deviations, committed diff, and test evidence.
   - Pass when material claims accurately describe the implementation and supported results.
   - Fail when the description contains material contradictions or unsupported claims.
   - Mark unclear when essential claims cannot be checked.

6. **Reviewer guidance**
   - Determine whether the PR gives reviewers useful context about its purpose, impact, and relevant trade-offs.
   - Pass when the context is sufficient for review.
   - Fail when important reviewer guidance is absent.
   - Mark unclear when the description is unavailable or incomplete.
   - This is a preferred check and never independently changes the verdict.

For each check, record:

- The exact check name from `rubric.md`.
- One grade: `pass`, `fail`, or `unclear`.
- One concise evidence line identifying the decisive fact or missing evidence.

Do not assign a grade based on writing polish alone. Do not treat an honestly disclosed shortfall as an automatic failure. Apply the check's stated pass condition.

Once the relevant evidence has been gathered and recorded, grade the check from that record without rereading unrelated parts of the package. Revisit a source only when the decisive evidence is ambiguous.

Complete every check even if an earlier required check fails.

## Verdict assembly

1. Review the completed check results in the original rubric order.

2. Apply the verdict rule from `rubric.md`:
   - Return `accept` only if every required check is `pass`.
   - Return `reject` if any required check is `fail` or `unclear`.
   - Preferred checks do not affect the final verdict.

3. Treat `unclear` on a required check as a blocking result. Do not assume missing evidence supports acceptance.

4. Identify the decisive check:
   - If the verdict is `reject`, select the first required check graded `fail` or `unclear` in rubric order.
   - Quote or identify that check's decisive evidence in the readable summary.
   - If multiple required checks fail, briefly report the other failures as well.
   - If the verdict is `accept`, identify the evidence supporting the required checks rather than inventing a failure.

5. Provide a readable per-check summary with concrete findings. In live mode, include any voice-guide violations separately and explain whether a rubric check makes them material.

6. End the response with the exact fenced JSON schema required by `SKILL.md` and `CONTRACT.md`.

7. Populate the `item` field with the PR URL or evaluation bundle ID. Include every rubric check in order, with its exact name, grade, and one-line evidence.

8. Use only `accept` or `reject` for the final verdict. Do not add extra JSON keys, change the schema, or append text after the closing JSON fence.

The same evidence and rubric grades must always produce the same verdict.
