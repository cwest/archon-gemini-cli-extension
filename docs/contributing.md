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

1.  Install `uv`:
    ```shell
    curl -LsSf https://astral.sh/uv/install.sh | sh
    ```
2.  Install dependencies:
    ```shell
    uv sync
    ```

### Task Runner

This project uses `poethepoet` for task management. The following tasks are available:

-   `uv run poe build`: Build the extension.
-   `uv run poe lint`: Run the linter.
-   `uv run poe format`: Format the code.
-   `uv run poe test`: Run the tests.

### Pre-commit Hooks

This project uses pre-commit hooks to enforce code quality. To install the hooks, run:
```shell
uv run pre-commit install
```

### Code Reviews

All submissions, including submissions by project members, require review. We
use [GitHub pull requests](https://docs.github.com/articles/about-pull-requests)
for this purpose.