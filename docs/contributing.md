# How to Contribute

We would love to accept your patches and contributions to this project.

## Before you begin

### Sign our Contributor License Agreement

Contributions to this project must be accompanied by a
[Contributor License Agreement](https://cla.developers.google.com/about) (CLA).
You (or your employer) retain the copyright to your contribution; this simply
gives us permission to use and redistribute your contributions as part of the
project.

If you or your current employer have already signed the Google CLA (even if it
was for a different project), you probably don't need to do it again.

Visit <https://cla.developers.google.com/> to see your current agreements or to
sign a new one.

### Review our Community Guidelines

This project follows [Google's Open Source Community
Guidelines](https://opensource.google/conduct/).

## Contribution process

### Local Development Setup

1.  Install [`uv`](https://docs.astral.sh/uv/):
    ```shell
    curl -LsSf https://astral.sh/uv/install.sh | sh
    ```
2.  Install dependencies:
    ```shell
    uv sync
    ```

### Task Runner

This project uses [`poethepoet`](https://github.com/nat-n/poethepoet) for task management. The following tasks are available:

-   `uv run poe build`: Build the extension.
-   `uv run poe lint`: Run the linter.
-   `uv run poe format`: Format the code.
-   `uv run poe test`: Run the tests.

### Pre-commit Hooks

This project uses [`pre-commit`](https://pre-commit.com/) hooks to enforce code quality. To install the hooks, run:
```shell
uv run pre-commit install
```

### Testing GitHub Actions Locally

To test GitHub Actions workflows locally, you can use [act](https://github.com/nektos/act). This tool allows you to run your workflows in a local Docker container.

**Prerequisites:**
-   Docker must be installed and running.

**Installation:**
-   **macOS:**
    ```shell
    brew install act
    ```
-   **Linux:**
    - Download the latest release from the [act releases page](https://github.com/nektos/act/releases).
-   **Windows:**
    - Use [winget](https://docs.microsoft.com/en-us/windows/package-manager/winget/) or [scoop](https://scoop.sh/):
      ```shell
      winget install nektos.act
      # or
      scoop install act
      ```

**Usage:**
1.  From the root of the project, run `act` to execute all workflows:
    ```shell
    act
    ```
2.  To run a specific workflow, use the `-W` flag with the path to the workflow file:
    ```shell
    act -W .github/workflows/ci.yml
    ```

### Code Reviews

All submissions, including submissions by project members, require review. We
use [GitHub pull requests](https://docs.github.com/articles/about-pull-requests)
for this purpose.