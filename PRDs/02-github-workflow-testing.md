# **Implementation Plan and Requirements Specification for GitHub Workflow Testing**

## **Part I: GitHub Workflow Testing - Requirements Specification**

### **1.0 Strategic Overview & System Vision**

#### **1.1 Project Mandate: Ensuring CI/CD Reliability and Developer Confidence**

The primary objective of this initiative is to establish a comprehensive and repeatable process for testing the project's GitHub Actions workflows. By leveraging local testing tools and a structured approach, we can validate the entire CI/CD pipeline, from basic linting and building to automated pull request creation and releases. This ensures that the workflows are robust, reliable, and function as expected before they are relied upon for production use. The goal is to create a clear, documented procedure that any developer can follow to verify workflow changes, thereby increasing developer confidence and maintaining a high standard of automation quality.

### **2.0 Core Principles**

The project will adhere to the following core principles:

1.  **Local-First Validation:** Workflows should be tested locally to the greatest extent possible to catch errors early and reduce reliance on live GitHub infrastructure for basic validation.
2.  **End-to-End Verification:** The testing process must cover the full lifecycle of each workflow, including interactions with the GitHub API for creating pull requests and releases.
3.  **Security and Best Practices:** The process must handle sensitive credentials like GitHub Personal Access Tokens (PATs) securely, ensuring they are never committed to the repository.
4.  **Clear Documentation:** The steps required to test the workflows must be clearly documented to enable any contributor to participate in the process.

### **3.0 Functional Requirements**

#### **3.1 Phase 1: Local Workflow Validation (Offline)**

-   **FR-1.1:** Execute the `ci.yml` workflow locally using `act`. All steps, including dependency installation, linting, and build script execution, must pass successfully.
-   **FR-1.2:** Execute the `sync.yml` workflow locally using `act`. All steps *except* the final "Create Pull Request" step must pass. The failure of the API-dependent step is expected and acceptable at this stage.
-   **FR-1.3:** Execute the `release.yml` workflow locally using `act` with a simulated tag push. All steps *except* the final "Create Release" step must pass. The failure of the API-dependent step is expected and acceptable at this stage.

#### **3.2 Phase 2: Setup for End-to-End Workflow Testing (Online)**

-   **FR-2.1:** The `.gitignore` file must be updated to include `.secrets` to ensure that local credential files are never committed to the repository.
-   **FR-2.2:** A local `.secrets` file shall be created to store the `GITHUB_TOKEN` for local testing with `act`.
-   **FR-2.3:** The project's local Git repository must be published as a new public repository on GitHub. The `gh` command-line tool is the preferred method for this task.
-   **FR-2.4:** Clear, step-by-step documentation must be created in `docs/contributing.md` detailing how a developer can generate a GitHub Personal Access Token (PAT) with the required `repo` and `workflow` scopes.

#### **3.3 Phase 3: End-to-End Workflow Execution (Online)**

-   **FR-3.1:** Execute the `sync.yml` workflow locally using `act`. With the `GITHUB_TOKEN` provided via the `.secrets` file, the workflow must run successfully and create a new pull request on the GitHub repository.
-   **FR-3.2:** Execute the `release.yml` workflow locally using `act` with a simulated tag push. With the `GITHUB_TOKEN` provided, the workflow must run successfully and create a new GitHub Release on the repository.

## **Part II: Implementation Plan**

### **4.0 Phase 1: Local Validation with `act`**

1.  **Install Docker and `act`:** Ensure both tools are installed and Docker is running.
2.  **Test `ci.yml`:**
    -   Open a terminal in the project root.
    -   Run the command: `act`
    -   Verify that all steps complete successfully.
3.  **Test `sync.yml`:**
    -   Run the command: `act workflow_dispatch -W .github/workflows/sync.yml`
    -   Verify that all build and linting steps pass.
    -   Confirm that the "Create Pull Request" step fails with an authentication error, as expected.
4.  **Test `release.yml`:**
    -   Run the command: `act push --tags -W .github/workflows/release.yml`
    -   Verify that the checkout step passes.
    -   Confirm that the "Create Release" step fails with an authentication error, as expected.

### **5.0 Phase 2: Preparing for End-to-End Testing**

1.  **Update `.gitignore`:**
    -   Append the line `.secrets` to the `.gitignore` file.
2.  **Create `.secrets` File:**
    -   Create a new file named `.secrets` in the project root.
    -   Add the following line, leaving the token blank for now: `GITHUB_TOKEN=`
3.  **Generate GitHub PAT:**
    -   Follow the documented steps (to be added to `docs/contributing.md`) to create a new GitHub Personal Access Token with `repo` and `workflow` scopes.
    -   Copy the generated token.
4.  **Populate `.secrets` File:**
    -   Paste the copied PAT into the `.secrets` file: `GITHUB_TOKEN=ghp_YourTokenHere`
5.  **Publish Repository to GitHub:**
    -   Ensure you are authenticated with the `gh` CLI (`gh auth login`).
    -   Run the command: `gh repo create archon-gemini-cli-extension --public --source=. --push`
    -   This will create a new public repository on your GitHub account and push the local code to it.

### **6.0 Phase 3: Executing End-to-End Tests**

1.  **Test `sync.yml` Pull Request Creation:**
    -   Run the command: `act workflow_dispatch -W .github/workflows/sync.yml`
    -   `act` will now use the token from `.secrets`.
    -   Verify that the workflow completes successfully.
    -   Check your GitHub repository to confirm that a new pull request titled "Automatic Sync with Archon Source" has been created.
2.  **Test `release.yml` Release Creation:**
    -   First, ensure there are no uncommitted changes created by the sync workflow test.
    -   Run the command: `act push --tags v0.0.1 -W .github/workflows/release.yml` (using a sample tag).
    -   Verify that the workflow completes successfully.
    -   Check your GitHub repository's "Releases" section to confirm that a new release titled "Release v0.0.1" has been created.
