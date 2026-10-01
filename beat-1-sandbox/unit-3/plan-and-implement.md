# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

YSWFelicity

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73#issuecomment-5939137423

I reproduced the mismatch between the setup instructions and `.env.example`.

My plan is to update `.env.example` so that the OpenRouter API key described in the README is represented in the example configuration. I’ll also verify how `LLM_PROVIDER` is handled in the code before changing the provider-options comment, so I don’t document `openrouter` as a supported value unless the repository behavior confirms it.

I’ll keep the change scoped to the configuration example and documentation mismatch in #73, without changing the LLM implementation or unrelated environment settings.

For verification, I’ll re-run the reproduction from my earlier report by copying `.env.example` to `.env` and checking the relevant API key and provider variables. The post-fix example should contain the OpenRouter configuration described by the setup documentation while preserving the existing mock development setup.

---

## Your branch

**Branch**

docs/73-align-openrouter-env-example

**Evidence**

Before:

Command:

```bash
cp .env.example .env
grep -n "OPENROUTER_API_KEY\|OPENAI_API_KEY\|LLM_PROVIDER" .env
grep -n "OPENROUTER_API_KEY" .env || echo "OPENROUTER_API_KEY not found in .env"
```

Output:

```text
18:LLM_PROVIDER=mock
19:OPENAI_API_KEY=sk-your-key-here
OPENROUTER_API_KEY not found in .env
```

After:

Command:

```bash
cp .env.example .env && grep -n "OPENROUTER_API_KEY\|OPENAI_API_KEY\|LLM_PROVIDER" .env
```

Output:

```text
18:LLM_PROVIDER=mock
19:OPENAI_API_KEY=sk-your-key-here
20:OPENROUTER_API_KEY=sk-or-your-key-here
```

The built change adds the missing `OPENROUTER_API_KEY` entry while preserving the existing mock and OpenAI configuration.

## Eval iterations

**Run history**

1. Full run: 19/20 scored items agreed.
2. Targeted run on `pkg-14`, `pkg-01`, and `pkg-07`: 3/3 scored items agreed.
3. Final full run: 20/20 scored items agreed.

The final full run reported:

```text
categories: clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4
agreement: 20/20 scored items  (bar: 18/20: PASS)
```

**Package analysis**

I analyzed `pkg-14`. The gold label was `accept`, but my first full run returned `reject` because the `diagnosis` check failed.

The candidate plan said:

> "on reattach, zellij issues OSC 4/10/11 color queries to the client terminal"

and proposed that the reattach path allowed those responses to reach the active pane.

The reproduction evidence strongly supported the reattach-specific behavior, but it did not fully prove every detail of that internal root cause. My original diagnosis check required the diagnosis to be "supported by the reproduced evidence," which was interpreted too strictly and caused the false reject.

I revised the check so that a diagnosis may be a plausible working explanation that is consistent with the reproduced evidence, as long as unsupported conclusions are not presented as established facts. After that revision, `pkg-14` correctly returned `accept`.

**Check rationale**

The final `diagnosis` check in my rubric is:

> "Pass if the diagnosis is consistent with the reproduced evidence and gives a plausible explanation for the observed behavior. The root cause does not need to be fully proven before implementation when the plan identifies what still needs verification and does not present unsupported conclusions as established facts."

I revised this from a stricter version that required the diagnosis to be "supported by the reproduced evidence." The first wording could reject a useful implementation plan simply because the exact internal root cause had not already been proven before implementation. The revised wording still rejects diagnoses that contradict the reproduction or state unsupported conclusions as facts, but it allows a grounded working diagnosis that the implementation will verify.

**Trade-offs**

Loosening the diagnosis check could potentially allow an unsupported root-cause theory to pass, so I re-ran `pkg-01` and `pkg-07` as `wrong-cause` canaries together with `pkg-14`.

The targeted run produced:

```text
pkg-01: reject
pkg-07: reject
pkg-14: accept

categories: clear-accept 1/1  wrong-cause 2/2
agreement: 3/3 scored items
```

This showed that the revision fixed the false rejection on `pkg-14` without changing the expected rejection of the two wrong-cause packages. The final full run then reached 20/20 agreement across all five categories.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
