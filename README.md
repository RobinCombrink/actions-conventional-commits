# actions-conventional-commits

Reusable GitHub Actions workflow that lints commit messages against
[Conventional Commits](https://www.conventionalcommits.org) using
[crate-ci/committed](https://github.com/crate-ci/committed). It is the CI twin of a local
`commit-msg` git hook: the hook gives instant feedback but is skippable and absent on fresh
clones — this workflow is the actual guarantee.

Part of a fleet of independently versioned reusable workflows (`actions-*` repos on this
account), released by
[actions-release](https://github.com/RobinCombrink/actions-release).

## Usage

`.github/workflows/conventional-commits.yml` in the calling repo:

```yaml
name: conventional-commits

on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: "${{ github.workflow }}-${{ github.ref }}"
  cancel-in-progress: true

jobs:
  lint:
    uses: RobinCombrink/actions-conventional-commits/.github/workflows/conventional-commits.yml@<commit-sha> # vX.Y.Z
```

Pin the exact commit SHA of the release you want (the `# vX.Y.Z` comment keeps it readable);
Dependabot bumps SHA and comment together.

## Behavior

- **Pull requests**: lints every commit in the PR range.
- **Pushes**: lints the pushed range (`before..after`). The first push of a brand-new ref is
  skipped — no before-range exists; those commits are covered by PR linting.
- The `committed` configuration is bundled inside the workflow — callers carry no
  `committed.toml`: conventional style, lowercase subjects, the full Conventional Commits
  type set (`feat fix docs style refactor perf test build ci chore revert`), bot authors
  ignored, merge commits not linted.
  A caller-local `committed.toml` overrides the bundled config if you need to diverge.
