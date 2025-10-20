# PRD: Python Packaging Best Practices

## 1. Strategic Overview

### Project Mandate
To align the project's packaging and release process with modern Python best practices, ensuring the `pyproject.toml` is comprehensive and correctly configured for use with `uv`.

### Core Principles
- **Standardization:** Adhere to modern Python packaging standards (PEP 518, PEP 621) to ensure predictable and reliable builds.
- **Completeness:** The `pyproject.toml` should be the single source of truth for project metadata, dependencies, and tool configuration.
- **Maintainability:** Centralize configuration to make the project easier to understand, maintain, and contribute to.

## 2. Problem Statement & User Need

The current `pyproject.toml` file is minimal and lacks essential metadata required for a compliant, professional, and maintainable open-source project. This sparseness creates several problems:

-   **For Contributors:** It increases onboarding friction, as key information like author details, project URLs, and classifiers is missing.
-   **For Users:** It can lead to inconsistent build and installation behavior because the build system isn't explicitly defined.
-   **For the Project:** It fails to adhere to modern Python packaging best practices, appearing unprofessional and potentially causing issues with packaging and distribution tools.

## 3. Target User Personas

1.  **The New Contributor:** Someone who has just cloned the repository. They want to understand the project's structure, dependencies, and development tools quickly so they can start contributing.
2.  **The Project Maintainer:** The person responsible for cutting new releases and ensuring the project's long-term health. They need a centralized, complete configuration to make packaging, releasing, and compliance straightforward and reliable.
3.  **The End User:** Someone who installs the extension via `gemini extensions install`. They expect a smooth, error-free installation and need the package to correctly declare its dependencies and metadata.

## 4. User Stories

-   **As a New Contributor,** I want a single, comprehensive `pyproject.toml` file, so that I can quickly understand the project's dependencies, tool configurations, and metadata without searching through multiple files.
-   **As a Project Maintainer,** I want to define all project metadata and build requirements in `pyproject.toml`, so that I can create and publish new releases reliably and consistently.
-   **As an End User,** I want the package to have complete and accurate metadata, so that `pip` (or `uv`) can resolve dependencies correctly and I can trust the package's origin and license.

## 5. Functional Requirements

1.  **Add a `[build-system]` table:**
    -   **Justification:** Adheres to PEP 518, ensuring consistent and reliable project builds by explicitly defining the build backend.
    -   **Details:** Specify `setuptools` as the build backend.
2.  **Complete the `[project]` metadata table:**
    -   **Justification:** Provides essential metadata for packaging tools (PEP 621), PyPI, and users, improving discoverability, compliance, and trust.
    -   **Details:** Add or complete the `authors`, `license`, `classifiers`, `keywords`, and `urls` fields.
3.  **Centralize tool configuration under the `[tool]` table:**
    -   **Justification:** Reduces root-level configuration files and creates a single source of truth for project settings, improving maintainability.
    -   **Details:** Add a default `[tool.ruff]` configuration section within `pyproject.toml`.

## 6. Non-Functional Requirements

-   **Performance:** The changes to `pyproject.toml` should not negatively impact the performance of `uv` commands like `uv pip sync` or `uv pip install`.
-   **Compatibility:** The new configuration must be fully compatible with the latest stable versions of `uv` and `pip`.

## 7. Success Metrics & KPIs

1.  **Valid Configuration:** The updated `pyproject.toml` is successfully parsed by `uv` without any errors or warnings.
2.  **Reliable Developer Setup:** A new contributor can set up their development environment with a single command (`uv sync`), which successfully installs all base and development dependencies.
3.  **Successful Artifact Generation:** The `uv run poe build` command (which executes `src/build.py`) runs successfully, generating the correct static files needed for a GitHub release.
4.  **Human-Readable Metadata:** The `pyproject.toml` file contains complete and accurate metadata (author, license, URLs), serving as a clear and professional source of information for any developer inspecting the project.

## 8. Risks and Dependencies

-   **Risk: Evolving Best Practices.** The Python packaging ecosystem evolves. New standards or tools might emerge, making our `pyproject.toml` configuration outdated in the future. (Mitigation: Periodically review and update the configuration as part of regular maintenance.)
-   **Dependency: `uv` and `setuptools`.** Our entire process relies on these external tools. A breaking change in a future version of either could affect our build process. (Mitigation: Pinning tool versions can provide stability, though it requires manual updates.)

## 9. Out of Scope

1.  **No PyPI Release:** This project will not be configured for, nor will it be published to, the Python Package Index (PyPI). Distribution is handled exclusively through GitHub Releases.
2.  **No `build.py` Script Changes:** This project is focused solely on the `pyproject.toml` configuration. The functionality of the `src/build.py` script itself will not be modified.
3.  **No CI/CD Workflow Changes:** The existing GitHub Actions workflows (`ci.yml`, `release.yml`) will not be altered.