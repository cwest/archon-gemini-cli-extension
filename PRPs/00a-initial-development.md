# Implementation Plan: Gemini Archon Workflow Extension

## Overview
This document outlines the implementation plan for the Gemini Archon Workflow Extension. The project's primary goal is to create a Gemini CLI extension that brings the systematic, plan-driven development methodology of the `archon-example-workflow` into the developer's terminal. This extension will enforce a structured "plan-first" workflow, orchestrate plan execution via an external Archon MCP server, and conclude with an automated validation and testing phase.

## Requirements Summary
- **FR-1: `/archon:create-plan` Command:** Must take a requirements file path, generate a detailed implementation plan by simulating codebase analysis and leveraging Archon RAG tools, and save the plan to the `PRPs/` directory.
- **FR-2: `/archon:execute-plan` Command:** Must take a plan file path, create a project and tasks in the Archon MCP server, and systematically execute each task, updating its status from `todo` to `review`.
- **FR-3: Utility Commands:** Implement supporting commands like `/archon:primer` for context, `/archon:kb` for knowledge base queries, and `/archon:project` for project interactions.
- **FR-4 & 5: Agentic Personas:** Simulate `codebase-analyst` and `validator` agents through structured prompts within the `GEMINI.md` context file to guide the Gemini model's behavior during planning and testing phases.
- **FR-8 & 9 & 10: Automated Build Process:** A Python script (`build.py`) will be created to automatically convert the source `archon-example-workflow` files (commands and agent contexts) into the required Gemini CLI extension format (`.toml` files and `GEMINI.md`).
- **FR-11: CI/CD and Automation:** Establish GitHub Actions for CI (linting, running build script) and CD (automated releases), including a mechanism to detect changes in the upstream Archon repository and create a PR with updates.
- **FR-12: User Setup:** The extension requires users to have the Archon MCP server running locally and configured in their Gemini CLI settings.

## Research Findings
### Best Practices
The implementation will follow the best practices outlined in the requirements document and confirmed by the Gemini CLI and `uv` documentation. This includes a "plan-first" development paradigm, state management offloaded to a dedicated server, and fully automated build/release pipelines. Dependency management will use `uv` with `pyproject.toml`.

### Technology Decisions
- **Build Script:** Python, managed with `uv` for high-performance dependency management.
- **Source File Parsing:** `GitPython` for repository cloning/pulling and `python-frontmatter` for parsing Markdown files.
- **CI/CD:** GitHub Actions for all automation, including linting, building, releasing, and cross-repository synchronization.
- **Extension Framework:** Gemini CLI Extension architecture, using `.toml` for commands and `GEMINI.md` for persistent model context.

## Implementation Tasks

### Phase 1: Project Foundation and Extension Scaffolding
1. **Setup Git Repository & Python Project**
   - Description: Initialize the Git repository and set up a Python project using `uv init`. This will create the `pyproject.toml` file for project and dependency management.
   - Files to create: `.gitignore`, `pyproject.toml`, `src/build.py` (initially empty).
   - Dependencies: None.
   - Estimated effort: 2 hours.

2. **Create `gemini-extension.json` Manifest**
   - Description: Define the core extension manifest file with its name, version, and context file name as specified in the requirements.
   - Files to create: `gemini-extension.json`.
   - Dependencies: Task 1.
   - Estimated effort: 30 minutes.

3. **Add Build Script Dependencies**
    - Description: Use `uv add` to add `GitPython` and `python-frontmatter` as project dependencies in `pyproject.toml`.
    - Files to modify/create: `pyproject.toml`, `uv.lock`.
    - Dependencies: Task 1.
    - Estimated effort: 30 minutes.

### Phase 2: Automated Conversion Script (`build.py`)
1. **Implement Source Ingestion**
   - Description: Add logic to `build.py` to clone the `coleam00/Archon` repository if not present locally, or pull the latest changes if it is, using the `GitPython` library.
   - Files to modify/create: `src/build.py`.
   - Dependencies: Phase 1.
   - Estimated effort: 3 hours.

2. **Implement Context Transformation (`GEMINI.md`)**
   - Description: Add logic to `build.py` to read `CLAUDE.md` and agent files (e.g., `codebase-analyst.md`) from the source repository, consolidate them, and generate the master `GEMINI.md` context file.
   - Files to modify/create: `src/build.py`.
   - Dependencies: Task 2.1.
   - Estimated effort: 4 hours.

3. **Implement Command Transformation (`.md` to `.toml`)**
   - Description: Add logic to `build.py` to parse all command Markdown files from the source, transform their content (replace placeholders, update tool call syntax), and write the corresponding `.toml` files to the `commands/archon/` directory.
   - Files to modify/create: `src/build.py`.
   - Dependencies: Task 2.1.
   - Estimated effort: 5 hours.

### Phase 3: CI/CD and Automation
1. **Create CI Workflow**
   - Description: Set up a GitHub Actions workflow (`ci.yml`) that triggers on push/pull_request. It will install dependencies with `uv sync`, lint the `build.py` script, run it, and verify that the generated files are committed and up-to-date.
   - Files to create: `.github/workflows/ci.yml`.
   - Dependencies: Phase 2.
   - Estimated effort: 4 hours.

2. **Create Release Workflow**
   - Description: Set up a GitHub Actions workflow (`release.yml`) that triggers on a new version tag (e.g., `v1.0.0`). It will create a GitHub Release with automated changelog notes.
   - Files to create: `.github/workflows/release.yml`.
   - Dependencies: Phase 1.
   - Estimated effort: 3 hours.

3. **Create Cross-Repository Sync Workflow**
   - Description: Set up a scheduled GitHub Actions workflow to check for updates in the source `coleam00/Archon` repository. If new commits are found, it will run the build script and automatically create a pull request with the updated extension files.
   - Files to create: `.github/workflows/sync.yml`.
   - Dependencies: Phase 2, Task 3.1.
   - Estimated effort: 6 hours.

## Codebase Integration Points
### New Files to Create
- `gemini-extension.json`: The core manifest file for the extension.
- `src/build.py`: The Python script for automating the conversion of source files.
- `pyproject.toml` / `uv.lock`: Python project and dependency management files.
- `.github/workflows/ci.yml`: Continuous Integration workflow.
- `.github/workflows/release.yml`: Continuous Deployment (Release) workflow.
- `.github/workflows/sync.yml`: Cross-repository synchronization workflow.
- `GEMINI.md`: (Generated) The master context file for the Gemini model.
- `commands/archon/*.toml`: (Generated) All the custom command definition files.

## Technical Design

### Architecture Diagram
```
/gemini-archon-workflow/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── release.yml
│       └── sync.yml
├── .gitignore
├── commands/
│   └── archon/
│       └── *.toml  # Generated by build script
├── gemini-extension.json
├── GEMINI.md       # Generated by build script
├── pyproject.toml
├── uv.lock
└── src/
    └── build.py
```

### Data Flow
1. **Sync Workflow (Scheduled/Manual):** Checks for updates in `coleam00/Archon` repo.
2. **`build.py` Execution:** If updates are found (or during CI), the script clones the source repo, reads the `.md` files, transforms them, and writes the `GEMINI.md` and `.toml` command files.
3. **User Command (`/archon:create-plan`):** User provides a requirements file. The Gemini CLI loads the extension, reads `GEMINI.md` and the relevant `.toml` prompt, and sends it to the Gemini model. The model executes the plan generation and instructs the CLI to write the output PRP file.
4. **User Command (`/archon:execute-plan`):** User provides a plan file. The process is similar, but the model now makes tool calls to the external Archon MCP server to manage projects and tasks.

## Dependencies and Libraries
- **`uv`**: For Python environment and package management.
- **`GitPython`**: To interact with the source Git repository.
- **`python-frontmatter`**: To parse Markdown files with YAML frontmatter.
- **`ruff`**: (Dev dependency) For linting the build script.

## Testing Strategy
- **CI Pipeline:** The primary testing strategy is automated via the `ci.yml` workflow. It ensures the build script runs without errors and that the generated output is consistent and committed.
- **Manual Testing:** After each release, manual testing will be performed by installing the extension and running the core `/archon:create-plan` and `/archon:execute-plan` commands against a sample requirements file.

## Success Criteria
- [ ] The `build.py` script successfully clones the source repository and generates all required extension files (`GEMINI.md`, `commands/**/*.toml`).
- [ ] The CI pipeline passes, verifying the build script's correctness and that all generated files are up-to-date.
- [ ] The CD pipeline successfully creates a new GitHub Release when a version tag is pushed.
- [ ] The cross-repository sync workflow successfully detects upstream changes and creates a pull request.
- [ ] The final extension can be installed in the Gemini CLI and the `/archon:create-plan` command successfully generates a plan.

## Notes and Considerations
- The success of this extension is critically dependent on the user having a local Archon MCP server running and correctly configured. The `README.md` must be extremely clear about this prerequisite.
- Prompt engineering will be an iterative process. The initial prompts generated by the build script may need refinement to achieve optimal performance with the Gemini 2.5 Pro model.

---
*This plan is ready for execution with `/execute-plan`*
