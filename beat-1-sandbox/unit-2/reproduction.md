# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

## Your identity upstream

**GitHub username**

YSWFelicity

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73#issuecomment-5818954544

Hi! I'd like to investigate this issue. I'll compare the API key and provider setup described in the README with the current .env.example, then follow the documented setup to see which configuration is actually required. I'll report back with the environment, steps, and what I observe.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73#issuecomment-5819187651

### Reproduction Report

I reproduced the documentation and environment configuration mismatch described in #73.

#### Environment

- macOS
- Repository: `YSWFelicity/pathreview-ai301-fa26-s3`

#### Steps

1. I checked `README.md`. The Quick Start instructions say to copy `.env.example` to `.env` and add `OPENROUTER_API_KEY`.

2. I checked `docs/SETUP.md`. It also tells users to set `OPENROUTER_API_KEY`.

3. I followed the documented setup command:

```bash
cp .env.example .env
```

4. I checked the LLM-related settings in the generated `.env`:

```bash
grep -n "OPENROUTER_API_KEY\|OPENAI_API_KEY\|LLM_PROVIDER" .env
```

Output:

```text
18:LLM_PROVIDER=mock
19:OPENAI_API_KEY=sk-your-key-here
```

5. I checked specifically for `OPENROUTER_API_KEY`:

```bash
grep -n "OPENROUTER_API_KEY" .env || echo "OPENROUTER_API_KEY not found in .env"
```

Output:

```text
OPENROUTER_API_KEY not found in .env
```

6. I also checked `core/config.py`. It defines both `openai_api_key` and `openrouter_api_key`, while the default `llm_provider` is `mock`.

#### Expected

The example environment file should be consistent with the setup documentation so that users can identify the required OpenRouter configuration.

#### Actual

Both `README.md` and `docs/SETUP.md` tell users to configure `OPENROUTER_API_KEY`, but copying `.env.example` produces an `.env` containing `LLM_PROVIDER=mock` and `OPENAI_API_KEY` with no `OPENROUTER_API_KEY` entry.

This reproduces the configuration/documentation mismatch described in #73.

## Eval iterations

**Run history**

- Full run 1: 19/20
- Targeted run 1 (`pkg-09,pkg-17`): 2/2
- Full run 2: 19/20
- Targeted run 2 (`pkg-09,pkg-17,pkg-20`): 3/3
- Final full run: 20/20

**Package analysis**

I analyzed `pkg-09`. The gold label was `accept`, but my first rubric decided `reject` because the `behavior-shown` check was too strict for an honest cannot-reproduce result. The package recorded a relevant reproduction attempt, included output showing `ONE` batches before `TWO` batches, and clearly explained that the exact trigger may not have been reached. I revised the check so an evidenced cannot-reproduce can pass when the attempt and its limitation are stated honestly. After the revision, `pkg-09` was correctly graded `accept`.

**Check rationale**

Current check from `rubric.md`:

> | behavior-shown | Artifacts such as output excerpts, logs, screenshots, or recorded observations, read against the behavior described by the issue; see `references/evidence-guide.md` → Behavior shown | Pass if a successful reproduction includes evidence demonstrating the behavior described by the issue rather than an adjacent failure. For a cannot-reproduce result, pass if the evidence records a relevant attempt and its observed behavior, even when the report identifies a limitation that may have prevented the exact trigger, as long as that limitation is stated honestly rather than presented as proof that the issue does not exist. | required |

I revised this check after `pkg-09` was incorrectly rejected. The earlier wording effectively required a cannot-reproduce case to prove that the exact trigger had been reached. The current wording allows an honest, evidenced attempt to pass while still requiring the report to state any limitation clearly.

**Trade-offs**

Loosening `behavior-shown` created a risk that a wrong-target or incomplete package could be accepted. I re-ran `pkg-17` as a canary because it claimed to reproduce a Windows Terminal crash even though the terminal stayed open; it correctly remained `reject`. I also re-ran `pkg-20`, the disclosure package, after strengthening the `conventions` check. `pkg-20` correctly remained `reject` because the repository required AI-use disclosure and the candidate package did not include it. The final full run scored 20/20.
