# **Implementation Plan and Requirements Specification for the Gemini Archon Workflow Extension**

## **Part I: Gemini Archon Workflow \- Requirements Specification**

### **1.0 Strategic Overview & System Vision**

#### **1.1 Project Mandate: Replicating a Systematic AI Development Workflow**

The primary objective of this project is to engineer a Gemini Command-Line
Interface (CLI) extension that encapsulates the systematic, plan-driven
development methodology demonstrated in the archon-example-workflow.1 The goal
is to transition developers from ad-hoc, unstructured prompting to a reliable,
repeatable, and auditable process for AI-assisted software development. By
leveraging the extensible architecture of the Gemini CLI, this initiative will
deliver a self-contained "workflow-in-a-box" that integrates best practices for
planning, execution, and validation directly into the developer's terminal
environment.2

The core value proposition is to provide a tool that enforces a structured
approach to AI-powered coding. Instead of relying on conversational memory, the
extension will guide the user and the AI model through a formal process of
converting requirements into a detailed plan, executing that plan in a trackable
manner, and verifying the output with automated tests. This brings a level of
engineering discipline and predictability to a process that is often exploratory
and inconsistent.

#### **1.2 Core Principles: Planning, Execution, and Validation**

The system is founded on three inviolable principles that must be reflected in
its architecture and functionality:

1. **Planning:** The workflow must enforce a "plan-first" paradigm. All
   development work begins with the /archon:create-plan command, which
   transforms high-level requirements into a granular, actionable implementation
   plan. This step is mandatory and precedes any code generation, mirroring the
   create-plan workflow from the source material.1
2. **Execution:** The system must provide a mechanism for the systematic,
   stateful execution of the generated plan. The /archon:execute-plan command
   will orchestrate this process, using an integrated task management system to
   track the status of each item from inception to completion. This mirrors the
   execute-plan command's dependency on an external task management server.1
3. **Validation:** The workflow must conclude with an integrated and automated
   validation phase. This step, mirroring the function of the validator agent,
   is not optional and serves as the final quality gate before the development
   cycle is considered complete.1 It ensures that all implemented code is
   accompanied by corresponding tests, verifying its correctness and
   functionality.

#### **1.3 High-Level System Architecture**

The system will be composed of three primary components that work in concert to
deliver the end-to-end workflow:

1. **Gemini CLI Extension Package:** This is the user-facing component,
   distributed as a single installable unit. It contains the
   gemini-extension.json manifest file, a GEMINI.md context file for persistent
   model instructions, and a series of custom command definitions in .toml
   files.3
2. **External Archon MCP Server:** The extension will be stateless and will rely
   on the user running the official Archon MCP server locally. This server
   provides the necessary API for project and task management, knowledge base
   access, and stateful tracking of the development workflow.6 Users will be
   required to have this server installed and configured as a prerequisite.
3. **Gemini 2.5 Pro Model:** This is the underlying generative AI engine that
   performs the reasoning, analysis, planning, and code generation tasks. The
   extension's prompts will be specifically engineered to leverage the advanced
   capabilities of this model, which is accessed natively through the Gemini
   CLI.8

This architectural approach simplifies the extension's development and
maintenance by offloading all state management to the existing, robust Archon
server. The extension's sole responsibility is to provide the Gemini model with
the correct prompts and context to interact with the Archon server's tools,
acting as a pure interface layer.

### **2.0 Functional Requirements**

#### **2.1 Command-Line Interface (CLI) Commands**

The extension will expose a set of namespaced custom commands, defined in .toml
files located in a commands/archon/ directory. This namespacing prevents
conflicts and logically groups the extension's functionality.11

##### **2.1.1 The /archon:create-plan Command Workflow**

- **FR-1.1:** The command must accept a single, mandatory argument: the file
  path to a requirements document. The invocation syntax will be
  /archon:create-plan \<path-to-requirements.md\>.1 This is supported by the
  {{args}} variable in the .toml prompt definition.11
- **FR-1.2:** The command's underlying prompt must instruct the Gemini model to
  execute the multi-step planning process detailed in the source create-plan.md
  file. This includes reading and analyzing the requirements, performing a
  simulated codebase analysis, and generating a detailed implementation plan in
  a structured Markdown format.1
- **FR-1.3:** The prompt will be enhanced to leverage the full suite of Archon's
  RAG tools. It will instruct the model to use rag_get_available_sources,
  rag_list_pages_for_source, and rag_read_full_page for deep, targeted research
  within specific knowledge sources, in addition to the general
  rag_search_knowledge_base and rag_search_code_examples tools.1
- **FR-1.4:** The command must write the generated plan to a file within a PRPs/
  directory in the user's current workspace, adhering to the conventions
  established in the source workflow.1

##### **2.1.2 The /archon:execute-plan Command Workflow**

- **FR-2.1:** The command must accept a single, mandatory argument: the file
  path to a previously generated implementation plan. The invocation syntax will
  be /archon:execute-plan \<path-to-plan.md\>.1
- **FR-2.2:** The command's prompt must orchestrate the entire task-driven
  development loop. This will involve a sequence of calls to the external Archon
  MCP server to (a) create a new project, (b) parse the plan and create all
  associated tasks with an initial 'todo' status, and (c) iterate through each
  task, directing the model to implement the code and update the task status
  from todo to doing and finally to review.1
- **FR-2.3:** The prompt must explicitly instruct the model to adopt the
  "validator" persona (defined in GEMINI.md) after all implementation tasks have
  been moved to the review status.
- **FR-2.4:** All interactions with the project and task management system must
  be performed exclusively through tool calls to the user-configured archon MCP
  server, as mandated by the critical rules translated from the CLAUDE.md file.1

##### **2.1.3 Supporting Utility Commands**

- **FR-3.1:** A utility command, /archon:primer, will be implemented. Its prompt
  will instruct the model to read key project files like README.md and GEMINI.md
  to establish context at the beginning of a session, mirroring the primer.md
  functionality.1
- **FR-3.2:** A command, /archon:kb, will be created to provide a user-facing
  interface to the Archon knowledge base. It will support sub-commands like
  /archon:kb search \<query\> (using rag_search_knowledge_base), /archon:kb
  sources (using rag_get_available_sources), and /archon:kb find-docs \<query\>
  (using find_documents).
- **FR-3.3:** A command, /archon:project, will be created for project-level
  interactions, such as /archon:project features \<project_id\> (using
  get_project_features) and /archon:project find \<query\> (using
  find_projects).

#### **2.2 Agentic Capabilities**

The archon-example-workflow defines specialized "subagents" like
codebase-analyst and validator in separate Markdown files.1 While the Gemini CLI
extension framework does not have a formal concept of subagents, the core
behavior of these agents is defined entirely by their prompt instructions.4 This
behavior can be effectively simulated through strategic prompt engineering. The
persona, mission, and methodology of each agent will be defined within the
global GEMINI.md context file. The primary command prompts (.toml files) will
then instruct the model to "assume the persona of the codebase-analyst" or "act
as the validator" at the appropriate stage of the workflow. This approach
achieves the desired functional separation without the architectural complexity
of a true multi-agent system, by guiding the single Gemini model to switch
contexts and roles as needed.

##### **2.2.1 Codebase Analysis Functionality**

- **FR-4.1:** As part of the /archon:create-plan command's execution, the prompt
  will contain a dedicated section that instructs the model to adopt the
  "codebase-analyst" persona. It must then perform the deep analysis specified
  in the codebase-analyst.md file, including discovering project structure,
  extracting coding patterns, analyzing component integrations, and identifying
  testing conventions.1 The findings of this analysis must be used to inform the
  generated implementation plan.

##### **2.2.2 Automated Validation and Test Generation**

- **FR-5.1:** During the final phase of the /archon:execute-plan command's
  workflow, the prompt will instruct the model to adopt the "validator" persona.
  The prompt will provide the model with the context of the newly implemented
  code (e.g., file paths that were modified).
- **FR-5.2:** The model must be directed to generate simple, effective unit
  tests for the new functionality, strictly following the guidelines outlined in
  the validator.md source file, such as focusing on the "happy path" and
  critical edge cases, and using the appropriate testing framework for the
  project.1

#### **Table 2.1: Functional Requirements Traceability Matrix**

| Source Feature (from archon-example-workflow)           | Corresponding FR ID(s) | Implementation Priority |
| :------------------------------------------------------ | :--------------------- | :---------------------- |
| Systematic planning via /create-plan                    | FR-1.1, FR-1.2, FR-1.4 | High                    |
| Enhanced RAG-based research                             | FR-1.3                 | High                    |
| Codebase analysis via codebase-analyst subagent         | FR-4.1                 | High                    |
| Generation of a comprehensive implementation plan (PRP) | FR-1.2, FR-1.4         | High                    |
| Systematic execution of plan via /execute-plan          | FR-2.1, FR-2.2, FR-2.4 | High                    |
| Automatic creation of Archon project and tasks          | FR-2.2                 | High                    |
| Task status tracking (todo $\\rightarrow$ done)         | FR-2.2                 | High                    |
| Validation with validator subagent to create unit tests | FR-2.3, FR-5.1, FR-5.2 | High                    |
| Project context loading via primer.md                   | FR-3.1                 | Medium                  |
| Knowledge base utility commands                         | FR-3.2                 | Medium                  |
| Project utility commands                                | FR-3.3                 | Medium                  |

## **Part II: Automated Source Conversion & Build Process**

### **3.0 Programmatic Conversion Strategy**

To ensure consistency and repeatability, the process of converting the source
archon-example-workflow files into the Gemini CLI extension format will be fully
automated. A build script will be developed to perform this translation, serving
as the single source of truth for generating the extension's core files.

#### **3.1 Toolchain Selection**

The build script will be implemented in **Python**, managed with **uv**. This
choice is driven by Python's strong ecosystem for file manipulation and data
parsing, combined with uv's high-performance dependency and environment
management.13 The script will leverage the following key libraries:

- **GitPython:** To programmatically clone or pull the latest version of the
  coleam00/Archon repository.15
- **python-frontmatter:** To parse the YAML frontmatter and Markdown content
  from the source files.18

#### **3.2 Source Ingestion**

The script will begin by ensuring it has the latest version of the canonical
workflow sources.

- **FR-8.1:** The script must clone the coleam00/Archon repository if it doesn't
  exist locally, or pull the latest changes from the stable branch if it does.16
- **FR-8.2:** The script will target the files within the
  archon-example-workflow/ directory of the cloned repository.

#### **3.3 Transformation Logic**

The core of the script will be a series of transformation functions that convert
the source files into Gemini CLI-compatible formats.

##### **3.3.1 Command Transformation (.md to .toml)**

- **FR-9.1:** For each Markdown file in
  archon-example-workflow/.claude/commands/, the script will generate a
  corresponding .toml file in the extension's commands/archon/ directory.
- **FR-9.2:** The description field from the source file's YAML frontmatter will
  be mapped directly to the description key in the output .toml file.18
- **FR-9.3:** The entire Markdown body of the source file will become the value
  for the prompt key in the .toml file.
- **FR-9.4:** The script must perform the following string replacements on the
  prompt content:
  - Replace the $ARGUMENTS placeholder with the Gemini CLI syntax: {{args}}.11
  - Convert all Archon MCP tool calls from the mcp\_\_archon\_\_\<tool_name\>
    format to the Gemini CLI format: tools.archon.\<tool_name\>.
  - Rephrase instructions like "Use the codebase-analyst agent" to "Adopt the
    'codebase-analyst' persona as defined in GEMINI.md."

##### **3.3.2 Context Transformation (.md files to GEMINI.md)**

- **FR-10.1:** The script will generate a single, consolidated GEMINI.md file
  for the extension's root directory.
- **FR-10.2:** The content of archon-example-workflow/CLAUDE.md will be the
  foundation of GEMINI.md. The "CRITICAL: ARCHON-FIRST RULE" will be preserved
  as the highest-priority instruction.1
- **FR-10.3:** The content from each agent file in
  archon-example-workflow/.claude/agents/ (e.g., codebase-analyst.md,
  validator.md) will be appended to GEMINI.md under distinct, clearly marked
  sections (e.g., \#\# Persona: codebase-analyst). The YAML frontmatter from
  these agent files will be discarded.1

#### **3.4 Build Script Output**

The script's execution will result in a complete, ready-to-use Gemini CLI
extension structure, which can then be committed to the project's Git
repository.

## **Part III: Gemini Archon Workflow \- Implementation Plan**

### **4.0 Phase 1: Project Foundation and Extension Scaffolding**

#### **4.1 Repository Setup and Tooling**

The initial phase focuses on establishing a robust project structure and
development environment. A new Git repository will be initialized. A Python
project will be set up using uv init, and dependencies for the build script
(GitPython, python-frontmatter) will be added using uv add.13

The directory structure will be organized according to the best practices for
Gemini CLI extensions, ensuring clarity and maintainability.3

/gemini-archon-workflow/  
├──.github/  
│ └── workflows/  
│ ├── ci.yml  
│ └── release.yml  
├──.gitignore  
├── gemini-extension.json  
├── GEMINI.md \# Generated by build script  
├── pyproject.toml \# Managed by uv  
├── uv.lock \# Managed by uv  
├──.venv/ \# Managed by uv  
└── src/  
 └── build.py \# The automated conversion script

#### **4.2 Defining the gemini-extension.json Manifest**

The gemini-extension.json file is the manifest that defines the extension's
identity and behavior for the Gemini CLI.3 Since the extension is now stateless,
the manifest will be significantly simpler.

#### **Table 4.1: gemini-extension.json Manifest Field Definitions**

| Field           | Type   | Description                                                               | Example Value     |
| :-------------- | :----- | :------------------------------------------------------------------------ | :---------------- |
| name            | String | A unique, kebab-case identifier for the extension. Used for namespacing.  | "archon-workflow" |
| version         | String | The semantic version of the extension (e.g., Major.Minor.Patch).          | "1.0.0"           |
| contextFileName | String | The name of the context file to load from the extension's root directory. | "GEMINI.md"       |

#### **4.3 Structuring the GEMINI.md Master Context File**

The GEMINI.md file serves as the global system prompt, providing persistent
instructions that influence the model's behavior whenever the extension is
active.12 This file will be automatically generated by the build script and will
contain:

1. **Critical Rules:** This section will house the translated "ARCHON-FIRST
   RULE" from the CLAUDE.md source file. It will unequivocally instruct the
   Gemini model to prioritize the user-configured archon MCP server for all task
   management operations.1
2. **Agent Personas:** This section will contain the detailed persona
   descriptions, methodologies, and output formats for both the
   "codebase-analyst" and the "validator," adapted directly from their
   respective .md files in the source material.1
3. **Tool Reference:** A summary of all 16 available Archon tools will be
   included to ensure the model is fully aware of its capabilities.

### **5.0 Phase 2: Core Command Implementation (via Build Script)**

This phase involves the creation and refinement of the build.py script that
handles the translation process.

#### **5.1 Implementing create-plan.md to /archon/create-plan.toml Translation**

The script will implement the logic defined in **FR-9.1** through **FR-9.4** to
convert create-plan.md into a valid .toml file. The script will use the
python-frontmatter library to load the source file and then apply the necessary
string transformations to the content before writing the final .toml output.18

#### **5.2 Implementing execute-plan.md to /archon/execute-plan.toml Translation**

This translation will follow the same process. The script will populate the
description and prompt from the execute-plan.md source file. A critical
adaptation will be updating the syntax for tool calls from
mcp\_\_archon\_\_manage_task to tools.archon.manage_task(...). The archon name
must exactly match the server name that users are instructed to configure in
their settings.json file.1

#### **5.3 Prompt Engineering for Gemini 2.5 Pro**

This task involves refining the translated prompts to be highly effective with
the Gemini 2.5 Pro model.8 The prompts generated by the script will be
engineered to be unambiguous, providing clear instructions and explicit
definitions of the expected output format. For instance, the prompt for
/archon:create-plan will specify the exact Markdown structure for the generated
plan, including headings and task formats, to ensure the output is consistent
and can be reliably parsed by the subsequent /archon:execute-plan command.

## **Part IV: Automated Build, Release, and Distribution**

### **6.0 CI/CD Pipeline Architecture using GitHub Actions**

#### **6.1 Linting and Conversion Script Execution**

A robust Continuous Integration (CI) pipeline is essential. A GitHub Actions
workflow, defined in .github/workflows/ci.yml, will be established to trigger on
every push and pull_request. It will consist of two sequential jobs:

1. **Setup and Lint:** This job will set up the Python environment using uv,
   install dependencies, and run a linter (e.g., ruff) to enforce coding
   standards on the build.py script.
2. **Run Conversion Script:** This job will execute the build.py script to
   generate the extension files. It will then run git diff \--exit-code to
   ensure that the committed versions of GEMINI.md and the .toml commands are
   up-to-date with the latest output of the build script. If the script
   generates any changes, the check will fail, enforcing that all generated
   files are committed.

#### **6.2 Triggering Conditions**

A separate Continuous Deployment (CD) pipeline, defined in
.github/workflows/release.yml, will manage the release process. This workflow
will be configured to trigger only when a new Git tag matching the semantic
versioning pattern v\*.\*.\* is pushed to the repository.23

#### **6.3 Secret Management for Release Tokens**

To create a GitHub Release, a fine-grained Personal Access Token (PAT) with repo
scope will be generated. This token will be stored securely as a repository
secret under the name GH_PAT, and the release.yml workflow will use this secret
for authentication.25

#### **6.4 Cross-Repository Update Automation**

To keep the extension synchronized with the source Archon repository, a
specialized GitHub Actions workflow will be implemented.

- **FR-11.1: Scheduled Trigger:** The workflow will be configured to run on a
  schedule (e.g., daily) using on: schedule and will also be manually
  triggerable via on: workflow_dispatch.26
- **FR-11.2: Upstream Change Detection:** The workflow will:
  1. Use a GitHub Action (e.g., nmbgeek/github-action-get-latest-commit) to
     fetch the latest commit SHA of the stable branch from the coleam00/Archon
     repository.28
  2. Compare this SHA against a stored value from the last successful run.
- **FR-11.3: Automated Pull Request Creation:** If the commit SHAs differ,
  indicating an update, the workflow will:
  1. Create a new feature branch (e.g.,
     feature/sync-archon-stable-\<short-sha\>).
  2. Run the build.py conversion script to generate the updated extension files.
  3. Use a dedicated action, such as peter-evans/create-pull-request, to commit
     the newly generated files and automatically open a pull request against the
     main branch for review.25

### **7.0 Versioning, Packaging, and Release Management**

#### **7.1 Semantic Versioning Strategy**

The project will strictly adhere to the Semantic Versioning 2.0.0 specification.
The version number, defined in the gemini-extension.json file, will serve as the
single source of truth.23

#### **7.2 Automated Release Creation and Changelog Generation**

The release.yml workflow will automate the creation of official GitHub Releases.
Upon a new version tag push, the workflow will use a community-standard action
(e.g., actions/create-release) to generate release notes from commit messages
and publish the release.32

#### **7.3 Packaging the Extension for Distribution**

Gemini CLI extensions are distributed directly from Git repositories.33 The
primary function of the release pipeline is to create a versioned, immutable Git
tag that points to a stable commit. Users will install the extension by
referencing the repository URL.

### **8.0 Distribution and User Installation**

#### **8.1 Hosting the Extension on GitHub**

The extension will be hosted in a public GitHub repository with a comprehensive
README.md that explains its purpose, features, and usage.

#### **8.2 User Installation Guide**

The README.md will provide users with a single command to install the extension
directly from GitHub:34

Bash

\# Install the latest version of the Archon Workflow extension  
gemini extensions install your-github-username/gemini-archon-workflow

#### **8.3 User Pre-requisites and Configuration**

The README.md will clearly state the pre-requisites for using the extension.

- **FR-12.1:** Users must have the Archon project installed and running locally
  as per its official documentation.7
- **FR-12.2:** Users must configure their global (\~/.gemini/settings.json) or
  workspace-level (.gemini/settings.json) Gemini CLI settings to include the
  Archon MCP server. The documentation will provide the exact JSON snippet
  required:36  
  JSON  
  {  
   "mcpServers": {  
   "archon": {  
   "httpUrl": "http://localhost:8051/mcp"  
   }  
   }  
  }

### **9.0 Conclusions and Recommendations**

This document outlines a comprehensive plan for developing a Gemini CLI
extension that successfully ports the structured, plan-driven methodology of the
archon-example-workflow. The key architectural decision to rely on an external,
user-managed Archon MCP server simplifies the extension, focusing its purpose on
providing a high-quality, model-specific interface to the Archon ecosystem.

The implementation's cornerstone is the automated Python-based conversion
script, managed with the modern uv toolchain. This script ensures that the
extension remains synchronized with its canonical source in a repeatable and
maintainable fashion. By combining this script with a sophisticated CI/CD
pipeline that includes cross-repository change detection, the project minimizes
manual effort and the risk of human error, ensuring long-term viability.

The final product will not only be a powerful utility for individual developers
but also a prime example of how to build sophisticated, maintainable, and
automated workflows on the Gemini CLI platform.

#### **Works cited**

1. coleam00-archon-ff3c2cf1bbe23064.txt
2. Gemini CLI | Gemini for Google Cloud, accessed October 19, 2025,
   [https://cloud.google.com/gemini/docs/codeassist/gemini-cli](https://cloud.google.com/gemini/docs/codeassist/gemini-cli)
3. Gemini CLI Tutorial Series — Part 11: Gemini CLI Extensions | by ...,
   accessed October 19, 2025,
   [https://medium.com/google-cloud/gemini-cli-tutorial-series-part-11-gemini-cli-extensions-69a6f2abb659](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-11-gemini-cli-extensions-69a6f2abb659)
4. Google's Open Source Gemini CLI Extensions Let Developers Build Custom
   AI-Powered Workflows \- InfoQ, accessed October 19, 2025,
   [https://www.infoq.com/news/2025/10/gemini-cli-extensions/](https://www.infoq.com/news/2025/10/gemini-cli-extensions/)
5. A Developer's Guide to Building Gemini CLI Extensions | by Kanshi Tanaike |
   Google Cloud \- Community | Oct, 2025 | Medium, accessed October 19, 2025,
   [https://medium.com/google-cloud/a-developers-guide-to-building-gemini-cli-extensions-5f72dcff4d29](https://medium.com/google-cloud/a-developers-guide-to-building-gemini-cli-extensions-5f72dcff4d29)
6. Archon: The Command Center for AI Coding Assistants | Joshua Berkowitz,
   accessed October 19, 2025,
   [https://joshuaberkowitz.us/blog/github-repos-8/archon-the-command-center-for-ai-coding-assistants-911](https://joshuaberkowitz.us/blog/github-repos-8/archon-the-command-center-for-ai-coding-assistants-911)
7. coleam00/Archon: Beta release of Archon OS \- the knowledge and task
   management backbone for AI coding assistants. \- GitHub, accessed October 19,
   2025,
   [https://github.com/coleam00/Archon](https://github.com/coleam00/Archon)
8. Gemini 2.5 Pro | Generative AI on Vertex AI \- Google Cloud, accessed October
   19, 2025,
   [https://cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/2-5-pro](https://cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/2-5-pro)
9. Google announces Gemini CLI: your open-source AI agent, accessed October 19,
   2025,
   [https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/)
10. Gemini Code Assist | AI coding assistant, accessed October 19, 2025,
    [https://codeassist.google/](https://codeassist.google/)
11. Gemini CLI: Custom slash commands | Google Cloud Blog, accessed October 19,
    2025,
    [https://cloud.google.com/blog/topics/developers-practitioners/gemini-cli-custom-slash-commands](https://cloud.google.com/blog/topics/developers-practitioners/gemini-cli-custom-slash-commands)
12. Collection of helpful gemini cli extensions and commands \- GitHub, accessed
    October 19, 2025,
    [https://github.com/philschmid/gemini-cli-extension](https://github.com/philschmid/gemini-cli-extension)
13. Python UV: The Ultimate Guide to the Fastest Python Package Manager \-
    DataCamp, accessed October 19, 2025,
    [https://www.datacamp.com/tutorial/python-uv](https://www.datacamp.com/tutorial/python-uv)
14. uv \- Python Developer Tooling Handbook, accessed October 19, 2025,
    [https://pydevtools.com/handbook/reference/uv/](https://pydevtools.com/handbook/reference/uv/)
15. GitPython Tutorial — GitPython 3.1.45 documentation, accessed October 19,
    2025,
    [https://gitpython.readthedocs.io/en/stable/tutorial.html](https://gitpython.readthedocs.io/en/stable/tutorial.html)
16. Automating some git commands with Python \- GeeksforGeeks, accessed October
    19, 2025,
    [https://www.geeksforgeeks.org/python/automating-some-git-commands-with-python/](https://www.geeksforgeeks.org/python/automating-some-git-commands-with-python/)
17. GitPython is a python library used to interact with Git repositories. \-
    GitHub, accessed October 19, 2025,
    [https://github.com/gitpython-developers/GitPython](https://github.com/gitpython-developers/GitPython)
18. Python Frontmatter — Python Frontmatter 1.0.0 documentation, accessed
    October 19, 2025,
    [https://python-frontmatter.readthedocs.io/](https://python-frontmatter.readthedocs.io/)
19. python-frontmatter \- PyPI, accessed October 19, 2025,
    [https://pypi.org/project/python-frontmatter/](https://pypi.org/project/python-frontmatter/)
20. Writing front matter using Python, accessed October 19, 2025,
    [https://blog.pesky.moe/posts/2024-07-23-python-frontmatter/](https://blog.pesky.moe/posts/2024-07-23-python-frontmatter/)
21. Google Gemini CLI Cheatsheet \- Philschmid, accessed October 19, 2025,
    [https://www.philschmid.de/gemini-cli-cheatsheet](https://www.philschmid.de/gemini-cli-cheatsheet)
22. Gemini CLI: Open-source AI agent. Write code, debug, and automate tasks with
    Gemini 2.5 Pro with industry-leading high usage limits at no cost. \-
    Reddit, accessed October 19, 2025,
    [https://www.reddit.com/r/ChatGPTCoding/comments/1lk6676/gemini_cli_opensource_ai_agent_write_code_debug/](https://www.reddit.com/r/ChatGPTCoding/comments/1lk6676/gemini_cli_opensource_ai_agent_write_code_debug/)
23. How GitHub Actions versioning system works \- Robs blog, accessed October
    19, 2025,
    [https://devopsjournal.io/blog/2022/10/19/How-GitHub-Actions-versioning-works](https://devopsjournal.io/blog/2022/10/19/How-GitHub-Actions-versioning-works)
24. Automating Releases with Semantic Versioning and GitHub Actions \- DEV
    Community, accessed October 19, 2025,
    [https://dev.to/arpanaditya/automating-releases-with-semantic-versioning-and-github-actions-2a06](https://dev.to/arpanaditya/automating-releases-with-semantic-versioning-and-github-actions-2a06)
25. Create Pull Request With GitHub Actions | Baeldung on Ops, accessed October
    19, 2025,
    [https://www.baeldung.com/ops/github-actions-create-pr](https://www.baeldung.com/ops/github-actions-create-pr)
26. How to trigger subsequent GitHub workflow in a different repository | Tech &
    Code with Kris, accessed October 19, 2025,
    [https://www.kristhecodingunicorn.com/post/trigger-github-workflow-in-different-repo/](https://www.kristhecodingunicorn.com/post/trigger-github-workflow-in-different-repo/)
27. Triggering Workflows in Another Repository with GitHub Actions | by Amina
    lawal \- Medium, accessed October 19, 2025,
    [https://medium.com/hostspaceng/triggering-workflows-in-another-repository-with-github-actions-4f581f8e0ceb](https://medium.com/hostspaceng/triggering-workflows-in-another-repository-with-github-actions-4f581f8e0ceb)
28. Get Latest Commit · Actions · GitHub Marketplace, accessed October 19, 2025,
    [https://github.com/marketplace/actions/get-latest-commit](https://github.com/marketplace/actions/get-latest-commit)
29. peter-evans/create-pull-request: A GitHub action to create a pull request
    for changes to your repository in the actions workspace, accessed October
    19, 2025,
    [https://github.com/peter-evans/create-pull-request](https://github.com/peter-evans/create-pull-request)
30. Create Pull Request · Actions · GitHub Marketplace, accessed October 19,
    2025,
    [https://github.com/marketplace/actions/create-pull-request](https://github.com/marketplace/actions/create-pull-request)
31. Automating Project Versioning and Release Process Using Semantic Versioning
    and GitHub Actions | by Niyi Alimi, accessed October 19, 2025,
    [https://niyialimi.medium.com/automating-project-versioning-and-release-process-using-semantic-versioning-and-github-actions-1a4cbf24fac2](https://niyialimi.medium.com/automating-project-versioning-and-release-process-using-semantic-versioning-and-github-actions-1a4cbf24fac2)
32. Managing releases in a repository \- GitHub Docs, accessed October 19, 2025,
    [https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository)
33. Extension Releasing | Gemini CLI, accessed October 19, 2025,
    [https://geminicli.com/docs/extensions/extension-releasing/](https://geminicli.com/docs/extensions/extension-releasing/)
34. Firebase extension for the Gemini CLI | Develop with AI assistance, accessed
    October 19, 2025,
    [https://firebase.google.com/docs/ai-assistance/gcli-extension](https://firebase.google.com/docs/ai-assistance/gcli-extension)
35. Now open for building: Introducing Gemini CLI extensions \- Google Blog,
    accessed October 19, 2025,
    [https://blog.google/technology/developers/gemini-cli-extensions/](https://blog.google/technology/developers/gemini-cli-extensions/)
36. How do I extend Gemini CLI with custom tools? \- Milvus, accessed October
    19, 2025,
    [https://milvus.io/ai-quick-reference/how-do-i-extend-gemini-cli-with-custom-tools](https://milvus.io/ai-quick-reference/how-do-i-extend-gemini-cli-with-custom-tools)
37. Hands-on with Gemini CLI \- Google Codelabs, accessed October 19, 2025,
    [https://codelabs.developers.google.com/gemini-cli-hands-on](https://codelabs.developers.google.com/gemini-cli-hands-on)
