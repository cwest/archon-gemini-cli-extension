# Implementation Plan: AI-Assisted PRD Generation

## Overview
This document outlines the implementation plan for a new Gemini CLI Extension command, `/archon:requirements`, designed to assist users in creating and refining Product Requirements Documents (PRDs). The implementation will consist of creating a single, sophisticated prompt file that manages multiple user workflows, from fully interactive sessions to automated document rewriting.

## Requirements Summary
- **FR-1.1:** A single, unified `/archon:requirements` command that adapts its behavior based on whether a file path is provided.
- **FR-2.1:** An interactive mode that embodies the `meta_prompt_collaborator` persona to guide users through PRD creation.
- **FR-2.2, FR-2.3:** The interactive mode must provide state summaries and offer to save progress at key checkpoints.
- **FR-2.4:** All workflows must conclude with a final user review of the PRD outline before writing the file.
- **FR-2.5:** The command must proactively use research tools (`web_fetch`, `google_web_search`, Archon RAG) to enrich the PRD content.
- **FR-2.6:** The prompt must be architected with clear states to manage the different workflows.
- **FR-3.1, FR-3.2:** The command must attempt to discover existing PRD directories and naming conventions.
- **FR-3.3:** The command must always receive explicit user confirmation before writing any files.

## Research Findings
### Best Practices
- The implementation will be a single `.toml` file in the `commands/archon/` directory, consistent with existing commands.
- The prompt will use the `{{args}}` variable to detect the presence of a file path, similar to `create-plan.toml`.
- The prompt's structure will be complex and stateful, requiring clear headings and instructions to guide the model's behavior, as established in the PRD (FR-2.6).

### Reference Implementations
- **`commands/archon/create-plan.toml`:** Serves as a reference for a complex, multi-step prompt that takes a file argument.
- **`meta_prompt_collaborator`:** The core logic for the interactive mode will be adapted from the persona and workflow defined at the [source URL](https://caseywest.com/images/posts/unlock-elite-agents-the-art-of-evolving-llm-prompts-into-system-masterpieces/prompt-to-write-better-prompts.md).

### Technology Decisions
- The feature will be implemented entirely within a single `.toml` file, leveraging the Gemini CLI's built-in prompt and tool-use capabilities. No new Python scripts or external dependencies are required.

## Implementation Tasks

### Phase 1: Foundation
1.  **Create Command File**
    -   **Description:** Create the new command file that will house the prompt and configuration for the `/archon:requirements` command.
    -   **Files to modify/create:** `commands/archon/requirements.toml`
    -   **Dependencies:** None
    -   **Estimated effort:** 15 minutes

### Phase 2: Core Implementation (Prompt Engineering)
1.  **Develop the Unified Prompt Structure**
    -   **Description:** Design the high-level structure of the prompt in `requirements.toml`. This includes creating sections for each state (`INITIALIZATION`, `AWAITING_WORKFLOW_CHOICE`, `INTERACTIVE_SESSION`, `AUTOMATIC_REWRITE_EXECUTION`, `FINAL_REVIEW`, `SAVE_FILE`).
    -   **Files to modify/create:** `commands/archon/requirements.toml`
    -   **Dependencies:** Task 1
    -   **Estimated effort:** 1 hour

2.  **Implement the Interactive Workflow Logic**
    -   **Description:** Write the detailed instructions for the `INTERACTIVE_SESSION` state. This involves translating the core logic of the `meta_prompt_collaborator` into the prompt, including the rules for disambiguation, providing state summaries, and offering to save progress.
    -   **Files to modify/create:** `commands/archon/requirements.toml`
    -   **Dependencies:** Task 2
    -   **Estimated effort:** 2 hours

3.  **Implement the Automatic Rewrite Logic**
    -   **Description:** Write the detailed instructions for the `AUTOMATIC_REWRITE_EXECUTION` state. This prompt section will guide the model to read a file, analyze it against the PRD template, perform research, and generate a revised version.
    -   **Files to modify/create:** `commands/archon/requirements.toml`
    -   **Dependencies:** Task 2
    -   **Estimated effort:** 1 hour

4.  **Integrate Filesystem and Research Logic**
    -   **Description:** Add instructions across all relevant states for the model to use tools like `glob` and `list_directory` to find PRD conventions, and to use research tools (`web_fetch`, `google_web_search`, etc.) to enrich content.
    -   **Files to modify/create:** `commands/archon/requirements.toml`
    -   **Dependencies:** Tasks 3, 4
    -   **Estimated effort:** 1 hour

### Phase 3: Integration & Testing
1.  **Manual End-to-End Testing**
    -   **Description:** Perform manual testing of all user workflows as defined in the PRD's "Testing and Refinement" section. This includes testing the interactive mode from scratch, the user-choice flow, and the automatic rewrite.
    -   **Files to modify/create:** None
    -   **Dependencies:** All previous tasks
    -   **Estimated effort:** 2 hours

2.  **Prompt Refinement**
    -   **Description:** Based on the results of the manual testing, refine the prompt in `commands/archon/requirements.toml` to address any issues, improve clarity, or make the agent's behavior more reliable.
    -   **Files to modify/create:** `commands/archon/requirements.toml`
    -   **Dependencies:** Task 6
    -   **Estimated effort:** 1 hour

## Codebase Integration Points
### Files to Modify
-   None

### New Files to Create
-   `commands/archon/requirements.toml`: This file will contain the entire implementation of the new feature.

### Existing Patterns to Follow
-   The command definition will follow the simple `description` and `prompt` key-value structure found in all `.toml` files in `commands/archon/`.
-   The prompt will use markdown headings (`##`) to structure its sections, similar to `create-plan.toml`.

## Technical Design

### State-Driven Prompt Architecture
The core of the technical design is a single, state-driven prompt. The model will be instructed to act as a state machine, transitioning between states based on user input and the presence of arguments.

```
[START] -> INITIALIZATION (Check for {{args}})
    |
    +-- (No {{args}}) -> INTERACTIVE_SESSION
    |
    +-- (Has {{args}}) -> AWAITING_WORKFLOW_CHOICE
                            |
                            +-- (User chooses "Interactive") -> INTERACTIVE_SESSION
                            |
                            +-- (User chooses "Automatic") -> AUTOMATIC_REWRITE_EXECUTION -> FINAL_REVIEW
                                                                    |
                                                                    +--> SAVE_FILE -> [END]

INTERACTIVE_SESSION -> (User completes section) -> Provide Summary -> Offer to Save -> INTERACTIVE_SESSION
    |
    +-- (User finishes) -> FINAL_REVIEW -> SAVE_FILE -> [END]
```

## Dependencies and Libraries
-   None. The implementation relies solely on the built-in capabilities of the Gemini CLI.

## Testing Strategy
-   **Interactive Mode (Creation):** Run `/archon:requirements` and follow the prompts to create a PRD from scratch. Verify that the agent asks clarifying questions and provides summaries.
-   **Interactive Mode (Refinement):** Run `/archon:requirements PRDs/some-notes.md` and choose the interactive option. Verify the agent correctly uses the file as a starting point.
-   **Automatic Mode:** Run `/archon:requirements PRDs/some-notes.md` and choose the automatic option. Verify the output is a well-structured and improved PRD.
-   **Filesystem Edge Cases:** Test in a directory with no `PRDs` folder to ensure the agent asks for a location. Test in a directory with an existing, unconventional naming scheme.

## Success Criteria
- [ ] A new file, `commands/archon/requirements.toml`, is created.
- [ ] The `/archon:requirements` command is available and functional in the Gemini CLI.
- [ ] The command correctly handles both no-argument and file-path invocations.
- [ ] The interactive mode successfully guides a user through the creation of a PRD, including state summaries and save points.
- [ ] The automatic mode successfully rewrites a draft PRD into a well-structured document.
- [ ] The command correctly discovers and uses existing PRD directory and naming conventions.
- [ ] The command always asks for user confirmation before writing a file.

## Notes and Considerations
-   The primary challenge of this implementation will be the complexity of the state-driven prompt. It must be meticulously crafted to ensure the model reliably follows the intended logic and transitions.
-   Thorough manual testing will be critical to identify and fix any confusing or unreliable behavior in the agent's conversational flow.

---
*This plan is ready for execution with `/archon:execute-plan`*
