# Implementation Plan: Python Packaging Best Practices

## Overview
This plan details the steps required to align the project's `pyproject.toml` with modern Python packaging best practices as specified in PRD-05. The goal is to create a single, comprehensive configuration file that serves as the source of truth for project metadata, build requirements, and tool settings.

## Requirements Summary
- **Standardize `pyproject.toml`:** Adhere to PEP 518 and PEP 621.
- **Add Build System:** Explicitly define `setuptools` as the build backend.
- **Complete Project Metadata:** Add authors, license, classifiers, keywords, and project URLs.
- **Centralize Tooling:** Move `ruff` configuration into `pyproject.toml`.

## Research Findings
### Best Practices
- **PEP 518 (`[build-system]`):** Specifies the build backend, ensuring consistent and reliable builds. `setuptools` is a standard and robust choice.
- **PEP 621 (`[project]`):** Defines a standard for project metadata, making it easily machine-readable for tools like `uv`, `pip`, and PyPI.
- **Tool Configuration:** Centralizing tool configurations (like `ruff`) under the `[tool]` table in `pyproject.toml` reduces the number of root-level configuration files and simplifies project structure.

### Reference Implementations
- The current `pyproject.toml` is minimal and will be the file modified.
- Ruff documentation provides clear guidance on configuring it within `pyproject.toml`.

### Technology Decisions
- **Build Backend:** `setuptools` will be used as it is a mature, widely-used, and reliable build backend for Python projects.
- **Linter:** `ruff` is already in use and will be configured with a default set of rules to enforce code quality and style.

## Implementation Tasks

### Phase 1: Configuration Update
1.  **Clean and Add Build System**
    -   **Description:** Remove the non-standard Apache License text commented at the top of `pyproject.toml`. Add the `[build-system]` table to specify `setuptools` as the build backend.
    -   **Files to modify/create:** `pyproject.toml`
    -   **Dependencies:** None
    -   **Estimated effort:** 15 minutes

2.  **Complete Project Metadata**
    -   **Description:** Populate the `[project]` table with complete and accurate metadata, including `authors`, `license`, `classifiers`, `keywords`, and `urls`.
    -   **Files to modify/create:** `pyproject.toml`
    -   **Dependencies:** None
    -   **Estimated effort:** 15 minutes

3.  **Centralize Ruff Configuration**
    -   **Description:** Add a `[tool.ruff]` table to `pyproject.toml` with a sensible default configuration for linting and formatting.
    -   **Files to modify/create:** `pyproject.toml`
    -   **Dependencies:** None
    -   **Estimated effort:** 15 minutes

## Codebase Integration Points
### Files to Modify
-   `pyproject.toml`: This is the only file that will be modified. The changes will involve removing the large license comment block and adding the `[build-system]`, updating `[project]`, and adding `[tool.ruff]` tables.

### New Files to Create
-   None.

### Existing Patterns to Follow
-   The plan follows standard Python packaging conventions.

## Technical Design
The changes are purely declarative and reside within the `pyproject.toml` file. No procedural code or architectural changes are required.

### `pyproject.toml` Structure
```toml
[build-system]
requires = ["setuptools"]
build-backend = "setuptools.build_meta"

[project]
name = "archon-gemini-cli-extension"
version = "0.1.0"
description = "An Archon-powered Gemini CLI extension for advanced, AI-driven software development workflows."
readme = "README.md"
requires-python = ">=3.14.0"
license = { file = "LICENSE" }
authors = [
  { name = "Casey West", email = "casey@caseywest.com" }
]
keywords = ["gemini", "cli", "extension", "archon", "ai"]
classifiers = [
    "Development Status :: 4 - Beta",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: Apache Software License",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.14",
]
dependencies = [
    "gitpython>=3.1.45",
    "python-frontmatter>=1.1.0",
]

[project.urls]
Homepage = "https://github.com/caseywest/archon-gemini-cli-extension"
Repository = "https://github.com/caseywest/archon-gemini-cli-extension"
"Bug Tracker" = "https://github.com/caseywest/archon-gemini-cli-extension/issues"

[dependency-groups]
dev = [
    "pre-commit>=4.3.0",
    "ruff>=0.14.1",
    "poethepoet>=0.27.0",
]

[tool.poe.tasks]
build = "python src/build.py"
lint = "ruff check ."
format = "ruff format ."
test = "pytest"

[tool.ruff]
line-length = 88

[tool.ruff.lint]
select = ["E", "F", "W", "I"]

[tool.ruff.format]
quote-style = "double"
```

## Dependencies and Libraries
- No new dependencies will be added. The plan only configures existing ones.

## Testing Strategy
- **Manual Validation:** After applying the changes, the following commands will be run to ensure the project remains functional:
    1. `uv pip sync`: To ensure all dependencies are installed correctly.
    2. `uv run poe lint`: To verify the new `ruff` configuration is working.
    3. `uv run poe build`: To confirm that the build script runs successfully with the new configuration.

## Success Criteria
- [ ] The `pyproject.toml` file is updated with the new `[build-system]` and `[tool.ruff]` tables.
- [ ] The `[project]` table in `pyproject.toml` is fully populated with accurate metadata.
- [ ] `uv pip sync` completes successfully.
- [ ] `uv run poe lint` and `uv run poe format` run without errors.
- [ ] `uv run poe build` completes successfully.

## Notes and Considerations
- The project is not intended for PyPI publication, so packaging configuration will not include PyPI-specific settings.
- This plan does not involve any changes to the CI/CD pipeline or the `src/build.py` script.

---
*This plan is ready for execution with `/archon:execute-plan`*
