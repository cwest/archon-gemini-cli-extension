# Archon Gemini CLI Extension

This extension integrates the Archon workflow directly into the Gemini CLI, providing a seamless experience for managing your projects and tasks.

## Prerequisites

Before you can use this extension, you must have the Archon MCP server running locally.

## Installation

1.  Install the extension using the `gemini` command:
    ```shell
    gemini extensions install https://github.com/cwest/archone-gemini-cli-extension
    ```

## Configuration

1.  Open your Gemini CLI `settings.json` file.
2.  Add the following configuration to connect to your local Archon MCP server:
    ```json
    {
      "mcpServers": {
        "archon": {
          "httpUrl": "http://localhost:8051/mcp"
        }
      }
    }
    ```

## Commands

This extension provides the following custom commands, listed in a logical workflow order:

- `/archon:primer`: Get the AI up to speed on the current project.
- `/archon:project-find`: Find projects by keyword.
- `/archon:project-features`: Get the features for a project.
- `/archon:requirements`: Create or refine a Product Requirements Document (PRD).
- `/archon:create-plan`: Create a comprehensive implementation plan from a requirements document.
- `/archon:execute-plan`: Execute a development plan with full Archon task management integration.
- `/archon:kb-sources`: List available sources in the Archon knowledge base.
- `/archon:kb-find-docs`: Find documents in the Archon knowledge base by keyword.
- `/archon:kb-search`: Search the Archon knowledge base for content using RAG.

## Getting Started

1.  Start your local Archon MCP server.
2.  Install and configure the extension as described above.
3.  Open the Gemini CLI and try one of the `/archon` commands, for example:
    ```shell
    /archon:project-find
    ```

## Usage Example with PRDs and PRPs

This repository contains examples of Product Requirements Documents (PRDs) and Project Requirement Plans (PRPs) in the `PRDs` and `PRPs` directories, respectively. PRPs are "Product Requirements Prompts", a concept for structuring requirements for AI agents. You can read more about them in [this article](https://abvijaykumar.medium.com/context-engineering-2-2-product-requirements-prompts-46e6ed0aa0d1).

### 1. Creating and Refining PRDs

The `/archon:requirements` command helps you write high-quality PRDs.

- **To create a new PRD from scratch**, run the command without arguments. The agent will guide you through a collaborative, step-by-step process:
  ```shell
  /archon:requirements
  ```
- **To refine an existing draft**, provide the path to your notes. The agent will ask if you want to start an interactive refinement session or have it perform an automatic rewrite:
  ```shell
  /archon:requirements PRDs/my-draft-notes.md
  ```

### 2. Generating an Implementation Plan

Once you have a PRD, you can use the `/archon:create-plan` command to generate an implementation plan (PRP):
```shell
/archon:create-plan PRDs/01-developer-experience-enhancements.md
```

### 3. Executing the Plan

After the plan is created, you can execute it with the `/archon:execute-plan` command:
```shell
/archon:execute-plan PRPs/01-developer-experience-enhancements.md
```