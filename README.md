# Sync Release Artifacts Action

A GitHub Action that keeps a target branch (default: `main`) in sync with release artifacts published on the release branch (default: `release`) after semantic-release. It either merges the changes or opens a pull request, depending on the input.

## What it does

- Fetches `main` and `release` branches
- Optionally opens a PR from `release` into `main` when `pr-needed: 'true'`
- Otherwise merges `release` into `main` and pushes the result
- Skips PR creation if no changes exist

## Usage

Example workflow:

~~~yaml
name: Update main branch
on:
  push:
    branches: [ release ]
    paths-ignore:
      - 'CHANGELOG.md'

permissions:
  contents: write
  pull-requests: write

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@9c091bb21b7c1c1d1991bb908d89e4e9dddfe3e0 # v7.0.0
        with:
          fetch-depth: 0

      - uses: knowledgepixels/sync-release-artifacts-action@3926beed487a1c183af52423344bf6ee36fd7b27 # v1.0.0
        with:
          main-branch: main
          release-branch: release
          pr-needed: false
          git-user-name: github-actions[bot]
          git-user-email: github-actions[bot]@users.noreply.github.com
~~~

## Inputs

| Name              | Description                                                                 | Required | Default                                              |
|-------------------|-----------------------------------------------------------------------------|----------|-------------------------------------------------------|
| `main-branch`     | Branch to update (target).                                                  | No       | `main`                                                |
| `release-branch`  | Branch to merge from (source).                                              | No       | `release`                                             |
| `pr-needed`       | If `true`, open a PR instead of pushing directly. Skips PR if no diff.     | No       | `false`                                               |
| `git-user-name`   | User name for commits/merges.                                               | No       | `github-actions[bot]`                                 |
| `git-user-email`  | Email for commits/merges.                                                    | No       | `github-actions[bot]@users.noreply.github.com`         |

## Permissions & requirements

- Ensure the workflow grants:
  - `contents: write` (required to push)
  - `pull-requests: write` (required when `pr-needed: 'true'`)
- Use `actions/checkout` with `fetch-depth: 0` (action expects a working repository).
- If `main` is protected, provide a token or allow the Actions bot to push, or use the PR flow.

## Testing

- Use a test workflow that prepares a `release` branch and runs the action with `pr-needed` set to `true` and `false`.
- A matrix job can run both flows in one dispatch: prepare `release` with a tiny change for the `true` run, keep it identical for the `false` run.

## Notes

- The action does not resolve real merge conflicts.
- When `pr-needed: 'true'` and there are no changes, PR creation is skipped with a clear log message.
- Consider branch protection and required checks when using the PR flow.

## License

[MIT](https://github.com/knowledgepixels/sync-release-artifacts-action/blob/main/LICENSE)
