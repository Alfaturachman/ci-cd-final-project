# Final Project Submission and Evaluation Guide

## CI/CD Tools and Practices - IBM DevOps and Software Engineering

This document is designed to guide you through the successful submission of your final project under the new Coursera evaluation system. It contains comprehensive details on what is graded, the exact formats required for submission, and a verification checklist.

---

## Grading System Overview

The Final Project is worth a total of **20 Points**. The passing grade is **70% (minimum 14 out of 20 points)**. Coursera offers two submission paths.

> [!TIP]
> **We highly recommend Option 1 (AI-Graded Submission)** because evaluation is processed instantly by Coursera's automated grading system, allowing you to receive your grade immediately without waiting for peer reviews.

### Submission Path Comparison

| Feature              | Option 1: AI-Graded Submission                 | Option 2: Peer-Graded Submission     |
| :------------------- | :--------------------------------------------- | :----------------------------------- |
| **Grading Method**   | Automated by Coursera AI Grader                | Evaluated by at least 3 course peers |
| **Grading Time**     | Instant (1-3 minutes)                          | 3 to 7 days depending on peer queue  |
| **Evidence Formats** | Public GitHub URLs + Text Logs + Image Uploads | Public GitHub URLs + Image Uploads   |
| **Graded Tasks**     | **8 Primary tasks**                            | **10 Detailed tasks**                |

---

## Task-by-Task Guide

Below are the step-by-step instructions for each deliverable you need to prepare.

### Group 1: Public GitHub Repository URLs (Tasks 1 - 3)

#### Task 1: GitHub README.md URL (2 Points)

- **What to submit:** The public GitHub URL pointing directly to your `README.md` file.
- **Format:**
  `https://github.com/{your_github_username}/ci-cd-final-project/blob/main/README.md`
- **Requirement:** Ensure your project name (`ci-cd-final-project`) is clearly listed under the project title section.

#### Task 2: GitHub Workflow YAML URL (4 Points)

- **What to submit:** The public GitHub URL pointing directly to your workflow definition file.
- **Format:**
  `https://github.com/{your_github_username}/ci-cd-final-project/blob/main/.github/workflows/workflow.yml`
- **Requirement:** The file must contain standard check steps, including `Lint with flake8` and `Run unit tests with nose`.

#### Task 3: Tekton Tasks YAML URL (4 Points)

- **What to submit:** The public GitHub URL pointing directly to your Tekton tasks definition file.
- **Format:**
  `https://github.com/{your_github_username}/ci-cd-final-project/blob/main/.tekton/tasks.yml`
- **Requirement:** The file must define both custom Tekton tasks: the `cleanup` task and the `nose` test task.

---

### Group 2: Evidence Files (Tasks 4 - 8 / 10)

For simple grading, capture screenshots from your development workspace or OpenShift console and save them with these exact filenames:

#### Task 4: OpenShift PersistentVolumeClaim (2 Points)

- **Evidence:** Screenshot of PVC details inside the OpenShift console.
- **Filename:** `oc-pipelines-console-pvc-details.png`
- **Details to show:** The PVC named `oc-lab-pvc` with a size of `1GB`, created under the `skills-network-learner` storage class, with the status set to **Bound**.

#### Task 5: GitHub Actions Validation (2 Points)

- **Evidence:** Terminal output log or a screenshot showing a successful GitHub Actions run.
- **Filename:** `cicd-github-validate.png` or `cicd-github-validate.txt`
- **How to retrieve text logs (recommended for Option 1):** Execute these commands in your Cloud IDE terminal and save the output:
    ```bash
    gh run list
    gh run view <run-id> --verbose
    ```

#### Task 6: OpenShift Pipeline Structure (2 Points)

- **Evidence:** Screenshot of the visual representation of your pipeline in the OpenShift console.
- **Filename:** `oc-pipelines-oc-final.png`
- **Details to show:** The sequential pipeline tasks: `cleanup` -> `git-clone` -> `flake8-linting` -> `nose-tests` -> `buildah` -> `deploy`.

#### Task 7: Successful OpenShift Pipeline Run (2 Points)

- **Evidence:** Screenshot of the pipeline console showing a successful execution.
- **Filename:** `oc-pipelines-oc-green.png`
- **Details to show:** All pipeline steps completed with green status icons (Succeed/Success).

#### Task 8: Running Application Logs (2 Points)

- **Evidence:** Console log output showing that the application is running successfully on the OpenShift cluster.
- **Filename:** `oc-pipelines-app-logs.png` or `oc-pipelines-app-logs.txt`
- **Details to show:** Flask or Node.js server log showing active port listening (`Running on http://...` or `SERVICERUNNING`).

---

## Submission Verification Checklist

Verify each of these items before submitting your final answers:

- [ ] **Public Repository Access:** Test your GitHub URLs in an incognito or private browser window. They must load successfully without prompt.
- [ ] **Correct Project Name:** Your `README.md` includes the name `ci-cd-final-project` clearly.
- [ ] **Lint and Test Configured:** The `workflow.yml` file contains active linting and testing steps.
- [ ] **Tekton Tasks Separation:** The `tasks.yml` file defines both `cleanup` and `nose` tasks, separated by `---`.
- [ ] **Exact Filenames:** All uploaded files match the required lowercase, hyphen-separated filenames.
- [ ] **PVC in Bound Status:** The screenshot `oc-pipelines-console-pvc-details.png` shows a status of Bound.
- [ ] **Green Pipeline Run:** No failures are present in the pipeline execution shown in `oc-pipelines-oc-green.png`.
- [ ] **Application Logs Running:** The console output contains server start details.

---

## Key Troubleshooting Tips

> [!NOTE]
> **Authentication Prompts During Push**
> When prompted for credentials during git pushes, use your **GitHub Personal Access Token (PAT)** rather than your standard account password. The PAT must have the `repo` and `write` scopes enabled.

> [!IMPORTANT]
> **Failing to Register Tekton Tasks**
> Ensure your active working directory is the repository root `/workspace/ci-cd-final-project` when executing:
>
> ```bash
> kubectl apply -f .tekton/tasks.yml
> ```

---

_Good luck with your submission. With this organized guide, you are fully prepared to secure a perfect 20/20 grade on Coursera._
