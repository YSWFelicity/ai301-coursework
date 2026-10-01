# Procedure: how this skill grades a plan package

## Read order

1. Read the issue context first. Note the reported problem, expected behavior, affected area, and any constraints or requests stated in the issue thread.

2. Read the reproduction evidence next. Note the environment, reproduction steps, observed behavior, and any limitations or uncertainty in what was reproduced.

3. Read the repo-facts block and any stated repository conventions. Note relevant files, implementation facts, contribution requirements, and communication requirements.

4. Read the proposed plan. Record its diagnosis, stated scope and exclusions, files to touch, implementation approach, test plan, risks, and unknowns.

5. Read the draft plan comment last. Note whether it accurately summarizes the proposed work and whether it follows the issue thread and repository conventions.

6. Keep the issue and reproduction evidence as the baseline when grading the plan. Do not treat an unsupported statement in the plan as established fact.

## Evidence gathering

1. For the diagnosis check, compare the plan's stated cause or explanation with the issue context and reproduction evidence. Record which observed facts support the diagnosis and which parts, if any, remain uncertain.

2. For the scope check, identify the files and behavior the plan proposes to change, along with anything it explicitly excludes. Compare that scope with the reproduced problem and repo facts to determine whether the work stays bounded to the issue.

3. For the approach check, record the concrete implementation actions proposed by the plan and the files or components they affect. Compare them with the supported diagnosis and repository facts to determine whether they address the relevant cause and are specific enough to begin implementing.

4. For the test-plan check, identify the commands, actions, or checks the plan proposes after the change. Compare them with the original reproduction steps and record the observable result the plan expects after the fix.

5. For the conventions check, compare the draft plan comment with the issue thread, repo-facts block, and any stated contribution or communication requirements. Record any required information, disclosure, or constraint and whether the comment satisfies it.

## Check execution

1. Grade `diagnosis` first using only the issue context, reproduction evidence, and the diagnosis stated in the plan. Pass it only when the diagnosis is supported by the evidence and uncertainty is not presented as certainty.

2. Grade `scope` next using the supported diagnosis, the plan's proposed files and exclusions, and repo facts. Pass it only when the proposed change is bounded to the reproduced problem and does not introduce unrelated work.

3. Grade `approach` using the plan's implementation actions, the supported diagnosis, and repo facts. Pass it only when the approach addresses the relevant cause and gives enough concrete direction for another contributor to begin the work without guessing an essential step.

4. Grade `test-plan` using the reproduction evidence and proposed tests. Pass it only when the test plan exercises the relevant behavior and defines an observable result that can distinguish the fixed state from the reproduced state.

5. Grade `conventions` last using the draft comment, issue thread, repo facts, and stated repository requirements. Pass it only when the comment accurately represents the plan and follows all applicable communication requirements.

6. If evidence needed for a required check is genuinely missing or too ambiguous to apply its pass condition, grade that check `unclear` rather than inventing or assuming the missing fact.

7. Once the evidence for a check has been gathered, grade that check from the recorded evidence and its rubric pass condition. Re-read other package sections only when a contradiction or dependency must be resolved.

## Verdict assembly

1. Collect the grade for every rubric check as `pass`, `fail`, or `unclear`.

2. Apply the verdict rule in `rubric.md`: accept only when every required check passes.

3. Treat `unclear` on a required check as a failure for the final verdict.

4. If any required check is `fail` or `unclear`, return `reject`. If all required checks pass, return `accept`.

5. In the output, include the evidence and reasoning used for each check. For a rejecting verdict, quote the package evidence that most directly supports the failed or unclear deciding check. Do not replace missing evidence with an assumption.
