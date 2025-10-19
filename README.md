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

This extension provides the following custom commands:

-   `/archon:create-plan`: Create a new implementation plan.
-   `/archon:execute-plan`: Execute an implementation plan.
-   `/archon:kb-find-docs`: Find documents in the knowledge base.
-   `/archon:kb-search`: Search the knowledge base.
-   `/archon:kb-sources`: List available knowledge base sources.
-   `/archon:primer`: Get a primer on the Archon workflow.
-   `/archon:project-features`: Get the features of a project.
-   `/archon:project-find`: Find a project.

## Getting Started

1.  Start your local Archon MCP server.
2.  Install and configure the extension as described above.
3.  Open the Gemini CLI and try one of the `/archon` commands, for example:
    ```shell
    /archon:project-find
    ```

## Usage Example with PRDs and PRPs

This repository contains examples of Product Requirements Documents (PRDs) and Project Requirement Plans (PRPs) in the `PRDs` and `PRPs` directories, respectively. PRPs are "Product Requirements Prompts", a concept for structuring requirements for AI agents. You can read more about them in [this article](https://abvijaykumar.medium.com/context-engineering-2-2-product-requirements-prompts-46e6ed0aa0d1).

Once you have a PRD, you can use the `/archon:create-plan` command to generate an implementation plan (PRP):
```shell
/archon:create-plan PRDs/01-developer-experience-enhancements.md
```

After the plan is created, you can execute it with the `/archon:execute-plan` command:
```shell
/archon:execute-plan PRPs/01-developer-experience-enhancements.md
```