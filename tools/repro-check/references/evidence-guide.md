# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: In an eval package, look at the repro report's environment record and compare it with the issue context and repo-facts block. In live mode, look at the student's repro draft and the repository's setup or contribution documentation.

What good looks like: The environment identifies the relevant OS, runtime, dependency, application, or tool versions needed to understand the reproduction. The versions should match the environment targeted by the issue, or the report should state any relevant differences.

## Steps

Where it lives: In an eval package, look at the reproduction steps in the repro report and read them against the issue context. In live mode, look at the steps in the student's draft repro comment and, when necessary, the repository documentation for setup commands or starting conditions.

What good looks like: The steps identify the starting state, the actions or commands performed, and the action that triggers the reported behavior. A stranger with the stated environment should be able to follow the same sequence without having to guess a missing action that is necessary to reach the result.

## Behavior shown

Where it lives: In an eval package, look at artifacts in the repro report such as command output, error excerpts, logs, screenshots, or other recorded observations, and compare them with the behavior described in the issue. In live mode, look at the evidence included in the student's repro draft and compare it with the issue thread.

What good looks like: The artifact shows the same behavior the issue describes, not merely a related warning, error, or different failure. The evidence should contain enough observable information to connect the result to the issue's reported behavior.

## Honesty

Where it lives: Compare the repro report's stated outcome with its steps and artifacts. In live mode, compare the student's claims in the draft comment with the evidence the student provides.

What good looks like: The stated result does not claim more than the evidence demonstrates. A successful reproduction is supported by evidence of the reported behavior. A cannot-reproduce result is also acceptable when the report records what was tried and the evidence shows that the reported behavior did not occur under that environment.

## Comms

Where it lives: In an eval package, look at the claim comment and repro report together with the issue context, repo-facts block, and any supplied contribution or AI-use policy. In live mode, compare the student's draft with the GitHub issue thread and the repository's CONTRIBUTING file, issue templates, and other stated contribution policies.

What good looks like: The claim refers to the specific issue and describes the investigation the contributor plans to perform without promising a fix, result, or completion date. The repro comment reports the observed result in the contributor's own words and follows any stated repository requirements, including required disclosure of AI assistance. If the repository states no special communication or disclosure requirement, the absence of one does not cause a failure.
