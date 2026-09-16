# Explicit Edit Benchmark official run

Use this template to run a verified Explicit Edit Benchmark observation on GitHub-hosted Actions with your own model and Hugging Face credentials. The benchmark implementation comes from an approved, immutable workflow commit; this repository contains no benchmark code.

A verified result means that the exact published bundle was produced and attested by the approved GitHub workflow. It does not prove the model provider's internal implementation. Local and ordinary Dataset contributions remain supported as unverified observations.

## 1. Create the repository

1. Select **Use this template** and create a **public** repository.
2. Open **Settings → Actions → General** and allow GitHub Actions.
3. Keep workflow permissions at their default. The workflows declare the narrow permissions they need.

Do not edit the pinned workflow SHAs. A newer template may use a newer approved SHA, but an older SHA remains usable while it is active in the benchmark release policy.

## 2. Add secrets

Open **Settings → Secrets and variables → Actions → New repository secret** and add:

- `PI_AUTH_JSON`: the complete Pi `auth.json` for the model account used by the run.
- `HF_TOKEN`: your Hugging Face user access token. It is used only to open a candidate pull request against `alexshpunt/explicit-edit-benchmark` and cannot publish to Dataset `main`.

Never put either value in workflow inputs, command arguments, repository variables, commits, or logs. The official workflow selects only the credential fields required by the registered provider and removes the temporary credential file after execution.

## 3. Run a partial observation

Open **Actions → Run official benchmark → Run workflow**.

The defaults run one registered task with:

- adapter `pi-default`;
- model `openai-codex/gpt-5.6-luna`;
- reasoning `low`;
- Pi `0.85.1`.

One task is a valid partial observation. A task failure, timeout, or low score is retained and does not block acceptance. Unsupported adapters, mutable package versions, missing credentials, and installation failures stop before publication.

For `pi-agent-ide`, select that adapter and provide its exact registered `harness_version`. Only adapters and exact package versions registered by the benchmark are accepted. Adding an adapter requires a reviewed code pull request to the benchmark repository.

## 4. Follow delivery and acceptance

The workflow has two jobs:

1. `run` produces, validates, and attests the exact result bundle;
2. `submit` sends those same bytes to a Hugging Face Dataset candidate, waits for the exact acceptance receipt, comments with the Dataset commit, and closes the candidate without rerunning inference.

The Dataset acceptance workflow verifies provenance and policy, appends the normalized observations, rebuilds leaderboard/views/badges atomically, and publishes with a scoped short-lived credential. No maintainer review is required for a valid official result.

Open the workflow summary to find the producer run and delivery result. Then check:

- [Dataset discussions](https://huggingface.co/datasets/alexshpunt/explicit-edit-benchmark/discussions) for the candidate;
- [Dataset history](https://huggingface.co/datasets/alexshpunt/explicit-edit-benchmark/commits/main) for the accepted commit;
- [Benchmark Explorer](https://huggingface.co/spaces/alexshpunt/benchmark-explorer) for the observation.

The accepted Dataset index records verification status, execution identity, source workflow, proof path, scoring revision, and exclusion policy.

## Delivery recovery without inference

If `run` succeeded but Hugging Face was unavailable, do not rerun the benchmark.

1. Copy the numeric run ID from the original **Run official benchmark** URL.
2. Open **Actions → Resubmit existing official result → Run workflow**.
3. Enter the original run ID and attempt.

The recovery workflow downloads the retained original archive and attestation, verifies their digest and execution identity, and retries only delivery. If the candidate already exists, it resumes from that candidate, verifies its accepted commit, and closes it. Submitting the same execution and digest is idempotent; a different bundle with the same execution identity is rejected as a conflict.

## Verify independently

Download the retained `official-benchmark-result` artifact from the producer workflow. The public proof contains the signed archive, attestation bundle, and transport metadata. The accepted proof is retained under `source/official/<executionId>/` in the Dataset after Actions artifact expiry.

The benchmark's verifier checks the artifact digest, exact signer workflow and approved SHA, GitHub-hosted execution, release policy, archive layout, normalized schema, task identities, packages, and configuration identities.

## Local and unverified runs

Local development is intentionally less restricted: custom configs, local packages, development extensions, and arbitrary task subsets remain available in the benchmark repository. Results submitted through the ordinary contribution path are stored as `unverified`; verified and unverified observations remain visible and filterable in the same Dataset.

This template has no hosted server and receives no maintainer model credentials. Computation runs in your GitHub Actions account, submission uses your HF token, and privileged Dataset publication runs only in the benchmark repository.
