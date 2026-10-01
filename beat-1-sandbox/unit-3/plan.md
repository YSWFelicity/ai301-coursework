# Implementation Plan for Issue #73

## Diagnosis

The reproduced problem is a configuration documentation mismatch between `README.md` and `.env.example`.

The reproduction showed that `README.md` tells users to add `OPENROUTER_API_KEY` to `.env`, while copying `.env.example` produces a configuration containing:

- `LLM_PROVIDER=mock`
- `OPENAI_API_KEY=sk-your-key-here`
- no `OPENROUTER_API_KEY`

The repository also defines `openrouter_api_key`, `openrouter_base_url`, and `openrouter_model` in `core/config.py`, and the review generator describes support for OpenAI or OpenRouter.

The evidence confirms that the example environment file does not reflect the OpenRouter setup described in the README. The current evidence does not establish that `LLM_PROVIDER=openrouter` is itself a supported provider value, so this plan will not assume that without further verification during implementation.

## Scope

The change will be limited to resolving the configuration guidance mismatch reported in issue #73.

In scope:

- Update `.env.example` so that its API-key guidance agrees with the documented OpenRouter setup.
- Update the provider-options comment only if repository behavior confirms that OpenRouter is a valid `LLM_PROVIDER` value.
- Keep the existing mock configuration available for development and testing.

Out of scope:

- Changing the LLM implementation.
- Refactoring provider selection.
- Changing OpenRouter models, URLs, or application behavior.
- Changing unrelated environment variables or documentation.

## Files to Change

Primary file:

- `.env.example`

`README.md` will only be changed if implementation-time verification shows that its existing OpenRouter instructions are inaccurate. Based on the current reproduction, the mismatch is primarily in `.env.example`.

## Approach

1. Verify how the application selects its LLM provider before changing the provider-options comment.
2. Add the missing OpenRouter API-key configuration guidance to `.env.example` so that a user following the README can see the expected variable in the example environment file.
3. If the code confirms `openrouter` as a supported `LLM_PROVIDER` value, add it to the documented provider options in `.env.example`. Otherwise, do not claim that value is supported and keep the change limited to the confirmed configuration mismatch.
4. Review the resulting diff to ensure the change remains limited to issue #73.

## Test Plan

Re-run the Unit 2 reproduction checks after the change.

Before the fix, copying `.env.example` and checking the relevant variables produced `LLM_PROVIDER=mock` and `OPENAI_API_KEY`, while:

`OPENROUTER_API_KEY not found in .env`

After the fix:

1. Copy `.env.example` to `.env`.
2. Search the copied `.env` for `OPENROUTER_API_KEY`, `OPENAI_API_KEY`, and `LLM_PROVIDER`.
3. Confirm that the OpenRouter API-key variable described by the README is now represented in the example configuration.
4. Confirm that any documented `LLM_PROVIDER` options match values verified in the repository.
5. Confirm that the existing mock configuration remains available and that no unrelated environment settings changed.

The post-fix result should no longer reproduce the original README/`.env.example` mismatch.

## Risks and Unknowns

The main unknown is whether `openrouter` is directly accepted as an `LLM_PROVIDER` value elsewhere in the application. The repository clearly contains OpenRouter configuration and an OpenRouter-compatible review generator, but the evidence gathered so far does not prove the provider-selection value itself.

To avoid introducing inaccurate documentation, the provider-options comment will only be changed after that behavior is verified.

The change is documentation/configuration-example focused, so application behavior should not otherwise change.

## Deviations

During implementation, I confirmed that the repository defines OpenRouter configuration, but I did not find code that uses `LLM_PROVIDER` to select `openrouter` as a provider value. Therefore, I left the existing provider-options comment unchanged rather than documenting an unsupported value. The implementation remained focused on the confirmed mismatch by adding the missing `OPENROUTER_API_KEY` entry to `.env.example`.
