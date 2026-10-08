# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

### Where it lives

In an eval bundle, read:

- The issue context and the plan-context block, especially the planned scope, intended behavior, changed-file boundaries, and documented deviations.
- The candidate PR's unified diff and commit list.
- The candidate PR's title and description, especially claims about implementing the plan exactly.

In live mode, read:

- The issue description and relevant comments on GitHub.
- `plan.md`, including `## Deviations`.
- The committed branch diff from `git diff main...HEAD`, run in the Path Review working copy.
- `pr_draft.md`, including its summary and changes sections.

### What good looks like

The material changes in the diff match the plan's intended behavior and scope, or any differences are accurately explained in the plan's deviation notes. The PR description makes claims consistent with the actual implementation.

Silent drift occurs when the diff adds unrelated behavior, omits planned material work without explanation, or contradicts the description's claim of exact plan fidelity. A smaller implementation is not automatically a failure when its limitations are honestly documented and the submitted change remains coherent.

## Test evidence (harness category: not-tested)

### Where it lives

In an eval bundle, read:

- The plan-context block's test plan and expected behavior.
- The original reproduction steps and expected before-and-after results, when provided.
- The candidate PR's test-evidence section, including commands, printed output, failures, and skipped checks.
- The candidate PR description's testing claims.
- The repo-facts block's required test commands or testing policy.

In live mode, read:

- The test plan in `plan.md`.
- The reproduction steps from the issue thread or earlier reproduction notes.
- `test_evidence.md`, including actual terminal commands and outputs.
- The Testing section of `pr_draft.md`.
- Repository testing instructions in `docs/CONTRIBUTING.md`, the PR template, and applicable project configuration.

### What good looks like

The evidence shows observable behavior relevant to the fix, identifies what was expected after the change, and provides actual results that support the PR's testing claims. Required repository checks have visible outcomes, or any limitations are accurately explained.

A statement such as "tests pass" without commands or observable results is not proof. A command that ran but failed must not be reported as passing. An honestly disclosed limitation may be acceptable when the remaining evidence sufficiently supports the submitted change and the applicable standards permit it.

## Diff quality (harness category: unreviewable)

### Where it lives

In an eval bundle, read:

- The candidate PR's unified diff.
- The commit list and changed-file summary.
- The issue context and plan scope to distinguish relevant changes from unrelated work.

In live mode, read:

- `git diff main...HEAD --stat` for the changed-file summary.
- `git diff main...HEAD` for the complete committed changes.
- The branch's commit history when needed to understand the included changes.
- `plan.md` and the issue to establish the intended boundary.

### What good looks like

The actual fix is visible in the diff, and each meaningful change supports the issue or a necessary implementation step. A reviewer can identify the intended change without sorting through unrelated modifications.

Warning signs include debug statements, temporary files, dead code, commented-out implementations, generated artifacts, unrelated refactors, broad formatting churn, and drive-by edits. A disclosed unrelated change can still make a PR unreviewable; disclosure alone does not make unrelated code appropriate.

## Standards and comms (harness category: standards-wall)

### Where it lives

In an eval bundle, read:

- The repo-facts block, including PR template requirements, contribution instructions, stated policies, and AI-use disclosure requirements.
- The issue context and any explicit maintainer directions.
- The candidate PR title and description, including issue linkage, testing details, and reviewer notes.

In live mode, read:

- `.github/PULL_REQUEST_TEMPLATE.md` for required PR sections and checklists.
- `docs/CONTRIBUTING.md` for contribution and testing standards.
- The issue thread for explicit maintainer requests.
- `pr_draft.md` for the title, description, issue linkage, testing checklist, and reviewer notes.
- `voice-guide.md` for personal communication rules that apply only in live mode.

### What good looks like

The PR provides the substantive information required by the repository, including a meaningful title, issue linkage, accurate testing information, relevant reviewer context, and AI-use disclosure when required. Explicit maintainer directions are addressed or their limitations explained.

Equivalent clear wording may satisfy a template requirement even when the formatting differs. Empty boilerplate, unsupported checked boxes, missing required disclosures, and ignored repository policies are evidence of a standards failure.

Description claims about what the diff actually implements belong to Plan fidelity; standards and comms focuses on whether the repository's stated submission requirements are satisfied.
