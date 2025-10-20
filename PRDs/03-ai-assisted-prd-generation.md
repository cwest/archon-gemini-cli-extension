# **Implementation Plan and Requirements Specification for AI-Assisted PRD Generation**

## **Part I: AI-Assisted PRD Generation - Requirements Specification**

### **1.0 Strategic Overview & System Vision**

#### **1.1 Project Mandate: Streamlining Requirements Definition with AI**

The primary objective of this project is to develop a new feature for the Gemini CLI Extension that assists users in creating high-quality, well-defined Product Requirements Documents (PRDs). This feature will encapsulate best practices for requirements gathering and documentation, tailored for the modern, AI-driven development lifecycle where PRDs are often transformed into Product Requirement Prompts (PRPs). By providing both an automated refinement mode and a deeply collaborative, interactive mode, the feature will reduce the friction of creating thorough requirements, improve the clarity of project goals, and ensure that the resulting PRDs are immediately actionable by AI coding assistants.

### **2.0 Core Principles**

The system will be designed around the following core principles:

1.  **Collaboration as a Core:** The feature will prioritize a partnership between the user and the AI, guiding the user through a structured process of discovery and refinement rather than just taking dictation.
2.  **Research-Driven:** The system will proactively use its available tools to enrich the PRD with external knowledge, best practices, and data-driven insights, ensuring the final document is comprehensive and well-informed.
3.  **Context-Awareness:** The tool will be mindful of the user's existing project, attempting to discover and adhere to established conventions for storing and naming documentation.
4.  **User Agency:** The user will always have the final say. The tool must ask for confirmation before writing any files or making significant decisions, ensuring the user remains in control of the process.
5.  **Optimized for AI Implementation:** The final output will be structured not just for human readability, but for optimal consumption by other AI agents (like the `/archon:create-plan` command), focusing on clarity, structure, and the explicit definition of goals, user stories, and acceptance criteria.

### **3.0 Functional Requirements**

#### **3.1 Command-Line Interface (CLI) Commands**

The feature will be exposed through a single, intelligent, namespaced command:

-   **FR-1.1: The `/archon:requirements` Command:**
    -   This command serves as the unified entry point for all PRD generation workflows. Its behavior adapts based on the provided arguments.
    -   **Interactive Creation Mode (No Arguments):** When invoked without arguments (`/archon:requirements`), it initiates a collaborative, multi-turn conversation to build a PRD from scratch.
    -   **User-Choice Mode (With File Path):** When invoked with a file path (`/archon:requirements <path-to-notes.md>`), the command will prompt the user to choose their desired workflow:
        1.  **Interactive Refinement:** Start a collaborative, multi-turn session to review, expand, and refine the contents of the provided file.
        2.  **Automatic Rewrite:** Autonomously analyze the provided document, restructure it according to best practices, enrich it with research, and write a new, improved version. This is for users who want a quick, AI-powered review and formatting pass.

#### **3.2 Agentic Capabilities and Workflow**

-   **FR-2.1: Collaborative Disambiguation Engine (Interactive Mode):**
    -   The interactive mode must implement the core logic of the `meta_prompt_collaborator` [1]. When it identifies an ambiguity or an incomplete section (e.g., missing user stories, unclear success metrics), it must:
        1.  Clearly state the decision point.
        2.  Suggest potential options to resolve it.
        3.  Provide pros and cons for each option.
        4.  Offer a recommendation based on best practices.
        5.  Wait for user input before proceeding.

-   **FR-2.2: Provide State Summaries (Interactive Mode):**
    -   At the completion of each major section of the PRD (e.g., after defining "User Stories"), the agent must provide a concise summary of the document's current state to keep the user oriented.

-   **FR-2.3: Offer to Save Progress (Interactive Mode):**
    -   After completing a major section and providing a summary, the agent must ask the user if they would like to save the current work-in-progress to a draft file. This provides a checkpoint and prevents data loss.

-   **FR-2.4: Final Review Step (All Modes):**
    -   Before writing the final PRD file, the agent must present a high-level summary or outline of the complete document for the user's final review and approval.

-   **FR-2.5: Proactive Research Module:**
    -   The command must leverage the full suite of available research tools.
    -   If URLs are present in the user's notes or draft, the system must use the `web_fetch` tool to incorporate their content.
    -   The system must use the `google_web_search` tool to research general best practices for the product domain or specific features mentioned.
    -   The system must utilize the Archon MCP server's RAG tools (`rag_search_knowledge_base`, `rag_search_code_examples`) to find relevant internal documentation or existing patterns that could inform the requirements.

-   **FR-2.6: State-Driven Prompt Architecture:**
    -   The underlying prompt for the unified command must be explicitly designed with distinct sections or "states" that manage the agent's behavior. This includes, but is not limited to, states for: `AWAITING_WORKFLOW_CHOICE` (when a file is provided), `INTERACTIVE_SESSION` (during collaborative refinement), and `AUTOMATIC_REWRITE_EXECUTION`. This ensures the agent can reliably manage the different workflows within a single command structure.

#### **3.3 Filesystem Interaction and Conventions**

-   **FR-3.1: PRD Location Discovery:**
    -   Before writing a new PRD, the system must attempt to identify the correct location for storing such documents. It will first look for a `PRDs/` directory. If not found, it will search for other common names like `docs/requirements/` or `product/`.
-   **FR-3.2: Naming Convention Adherence:**
    -   The system will scan the identified PRD directory for existing files to determine the naming convention (e.g., `##-feature-name.md`, `YYYY-MM-DD-feature.md`). It will then propose a name for the new file that follows this convention.
-   **FR-3.3: User Confirmation Workflow:**
    -   If a conventional location or naming scheme cannot be determined with high confidence, the system must ask the user where to save the file and what to name it.
    -   In all cases, before writing the file to disk, the system must present the proposed file path and name to the user and ask for explicit confirmation.

#### **3.4 PRD Content and Structure**

-   **FR-4.1: AI-Optimized PRD Template:**
    -   The system will generate PRDs based on a modern, AI-friendly template that includes, but is not limited to, the following sections:
        -   **Strategic Overview:** (Project Mandate, Core Principles)
        -   **Problem Statement & User Need:** (The "why" behind the feature)
        -   **Target User Personas:** (Who is this for?)
        -   **User Stories:** (In the "As a..., I want..., so that..." format)
        -   **Functional Requirements:** (Clear, itemized list of what the system must do)
        -   **Non-Functional Requirements:** (Performance, security, scalability)
        -   **Success Metrics & KPIs:** (How will we measure success?)
        -   **Risks and Dependencies:** (What could go wrong?)
        -   **Out of Scope:** (What are we explicitly not building?)

## **Part II: Implementation Plan**

### **4.0 Phase 1: Command Scaffolding and Prompt Engineering**

1.  **Create the Unified Command File:**
    -   Create a single new command file: `commands/archon/requirements.toml`.
2.  **Develop the Unified `requirements` Prompt:**
    -   This prompt will be engineered to handle all modes of operation.
    -   It will begin by checking for the presence of `{{args}}`.
    -   **If no args are present:** It will trigger the interactive creation workflow, instantiating the `meta_prompt_collaborator` persona and guiding the user from scratch.
    -   **If args are present:** It will first prompt the user to choose between "Interactive Refinement" and "Automatic Rewrite." Based on the user's choice, it will proceed with the appropriate workflow.
    -   The prompt will incorporate the logic for state summaries (FR-2.2), saving progress (FR-2.3), and the final review step (FR-2.4) within the interactive workflow sections.

### **5.0 Phase 2: Implementing Core Logic**

1.  **Filesystem and Convention Logic:**
    -   The logic for discovering PRD directories and naming conventions will be embedded within the unified prompt. The model will be instructed to use tools like `list_directory` and `glob` to inspect the user's workspace and infer the correct conventions before proposing a save location.
2.  **Research Integration:**
    -   The prompt will contain explicit instructions to use `web_fetch`, `google_web_search`, and the Archon RAG tools at appropriate points in both the interactive and automatic workflows.
3.  **Stateful Interaction Logic:**
    -   The prompt will be carefully structured to manage the multi-turn conversation. It will instruct the model to maintain the state of the PRD being built and to present summaries and save-prompts at the end of major sections.

### **6.0 Phase 3: Testing and Refinement**

1.  **Test All Modes:**
    -   Run `/archon:requirements` with no arguments to test the interactive creation workflow.
    -   Run `/archon:requirements <path-to-file>` and select the interactive refinement option.
    -   Run `/archon:requirements <path-to-file>` and select the automatic rewrite option.
2.  **Verify Stateful Features:**
    -   During interactive tests, confirm that state summaries are presented correctly and that the "save progress" prompts appear as expected.
    -   Confirm that the final review outline is presented before the file is written in all modes.
3.  **Test Filesystem Logic:**
    -   Test the command in projects with different directory structures to ensure the discovery logic is robust.
    -   Verify that the user is always prompted correctly before any file is written.

### **7.0 Works Cited**

1.  meta_prompt_collaborator, accessed October 20, 2025, https://caseywest.com/images/posts/unlock-elite-agents-the-art-of-evolving-llm-prompts-into-system-masterpieces/prompt-to-write-better-prompts.md
