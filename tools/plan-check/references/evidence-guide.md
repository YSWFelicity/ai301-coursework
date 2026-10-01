# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: In an eval package, compare the candidate plan's diagnosis with the issue context and the repro-evidence block. In live mode, compare the diagnosis in `plan.md` with the GitHub issue thread and the student's posted reproduction comment.

What good looks like: The stated diagnosis explains behavior that the reproduction evidence actually shows and does not contradict that evidence. If the evidence does not establish a root cause, the plan identifies the cause as uncertain rather than presenting it as confirmed.

## Scope

Where it lives: In an eval package, look at the candidate plan's scope statement, exclusions, named files or areas, and the repo-facts block. In live mode, look at the scope and files-to-touch sections of `plan.md` and compare them with the reproduced issue and relevant repository files.

What good looks like: The proposed work is bounded to the reproduced problem and identifies the files or areas that need to change. Unrelated cleanup, redesign, or additional features are excluded unless the evidence shows they are necessary to solve the issue.

## Executability

Where it lives: In an eval package, look at the candidate plan's implementation approach, files or areas to change, and repo-facts block. In live mode, look at the approach and files-to-touch sections of `plan.md` and verify relevant repository facts when necessary.

What good looks like: The plan identifies concrete changes and where they will be made. Another contributor could begin implementing the plan without having to guess the essential file, behavior, or action that must change.

## Test plan

Where it lives: In an eval package, compare the candidate plan's test plan with the repro-evidence block, especially its steps, commands, artifacts, and observed behavior. In live mode, compare the test plan in `plan.md` with the student's posted reproduction comment and the repository behavior being changed.

What good looks like: The test plan exercises the behavior demonstrated by the reproduction and states an observable expected result after the change. The result should distinguish the fixed behavior from the previously reproduced behavior rather than merely saying that tests should pass.

## Honesty

Where it lives: In an eval package, look at the diagnosis, risks, unknowns, and any deviations recorded in the candidate plan, then compare those statements with the repro evidence and repo facts. In live mode, look at the risks, unknowns, and `## Deviations` section of `plan.md` together with the evidence gathered during reproduction and implementation.

What good looks like: Confirmed facts, hypotheses, risks, and unknowns are clearly distinguished. The plan does not claim certainty beyond the evidence, and any difference between the posted plan and the actual build is recorded under `## Deviations` rather than hidden.

## Comms

Where it lives: In an eval package, read the candidate plan comment against the issue context, thread highlights, repo-facts block, and any stated contribution or AI-use policy. In live mode, compare `comment.md` with the GitHub issue thread and the repository's CONTRIBUTING file, templates, documentation, and other stated contribution requirements.

What good looks like: The comment is specific to the issue, accurately represents the proposed plan, and responds to relevant maintainer or thread guidance. It follows any stated repository requirements, including required AI-use disclosure when applicable, without making unsupported claims or promises.
