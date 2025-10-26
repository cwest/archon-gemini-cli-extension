# Release Process

This project uses GitHub Actions to automate the release process.

## Prerequisites

- You must have write access to the repository to trigger release workflows.

## Steps to Release

1.  **Trigger Preparation Workflow:**
    - Go to the [Actions tab](https://github.com/cwest/archon-gemini-cli-extension/actions) on GitHub.
    - Select the **Prepare Release** workflow from the left sidebar.
    - Click **Run workflow**.
    - Choose the version bump type (`patch`, `minor`, or `major`). Default is `minor`.
    - Click **Run workflow**.

2.  **Review and Merge Pull Request:**
    - The workflow will create a Pull Request titled `🔧 chore(release): prepare next release`.
    - Review the changes (version bump in `pyproject.toml` and `CHANGELOG.md` updates).
    - Merge the Pull Request into `main`.

3.  **Automated Tag and Release:**
    - Merging the PR will trigger the **Tag Release** workflow.
    - This workflow will create a git tag (e.g., `v0.2.0`).
    - Pushing the tag will trigger the **Release** workflow, which creates a GitHub Release and attaches build artifacts.

## Workflows Involved

- `.github/workflows/prepare-release.yml`: Bumps version, updates changelog, creates PR.
- `.github/workflows/tag-release.yml`: Triggers on push to `main` with release commit message, creates git tag.
- `.github/workflows/release.yml`: Triggers on tag push, creates GitHub Release.
