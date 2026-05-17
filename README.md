# CI/CD Tools and Practices: Final Project

[![DevOps](https://img.shields.io/badge/IBM-DevOps%20%26%20Software%20Engineering-blue)](https://www.coursera.org/professional-certificates/ibm-devops-and-software-engineering)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions%20%26%20Tekton-orange)](#)
[![OpenShift](https://img.shields.io/badge/OpenShift-Red%20Hat-red)](https://www.redhat.com/en/technologies/cloud-computing/openshift)
[![Coursera](https://img.shields.io/badge/Coursera-Final%20Project-blueviolet)](#)

Welcome to the Final Project repository for the Continuous Integration and Continuous Delivery (CI/CD) course. This repository has been structured and cleaned up to make it professional, readable, and easy to navigate for your final submission on Coursera.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Final Project Scenario](#final-project-scenario)
- [Prerequisites and Environment Initialization](#prerequisites-and-environment-initialization)
- [Part 1: CI Pipeline with GitHub Actions](#part-1-ci-pipeline-with-github-actions)
    - [Exercise 1: Create Basic Workflow](#exercise-1-create-basic-workflow)
    - [Exercise 2: Add Linting Step (Flake8)](#exercise-2-add-linting-step-flake8)
    - [Exercise 3: Add Unit Testing Step (Nose Tests)](#exercise-3-add-unit-testing-step-nose-tests)
    - [Exercise 4: Validate GitHub Actions Workflow](#exercise-4-validate-github-actions-workflow)
- [Part 2: CD Pipeline with Tekton and OpenShift](#part-2-cd-pipeline-with-tekton-and-openshift)
    - [Exercise 5: Create Tekton Cleanup Task](#exercise-5-create-tekton-cleanup-task)
    - [Exercise 6: Create Tekton Test Task (Nose)](#exercise-6-create-tekton-test-task-nose)
    - [Exercise 7: Create OpenShift Pipeline and Deployment](#exercise-7-create-openshift-pipeline-and-deployment)
- [Submission and Evaluation Documentation](#submission-and-evaluation-documentation)
    - [Option 1: AI-Graded Submission (20 Points)](#option-1-ai-graded-submission-20-points)
    - [Option 2: Peer-Graded Submission (20 Points)](#option-2-peer-graded-submission-20-points)
    - [Evidence File Naming Guidelines](#evidence-file-naming-guidelines)

---

## Project Overview

In this final project, you will apply the CI/CD concepts and skills you have learned throughout the course. Using a provided sample application and access to an OpenShift Cluster, you will build and deploy a complete CI/CD pipeline.

### Main Objectives:

1. **GitHub Actions**: Build an automated CI workflow to perform syntax checks (linting) and unit testing.
2. **Tekton**: Create standalone tasks for managing pipeline workspace resources.
3. **OpenShift Pipelines**: Chain the Tekton tasks into a complete CD pipeline to deploy the application to the OpenShift cluster.

---

## Final Project Scenario

You are part of a development team responsible for a RESTful API called the **Counters Service** (a microservice to manage and track counters). The frontend team has completed the user interface, and your role is to ensure the reliability, code quality, and automated deployment of the backend microservice using CI/CD best practices.

---

## Prerequisites and Environment Initialization

### 1. Create Your GitHub Repository

1. Visit the official template: [vselh-ci-cd-final-project-template](https://github.com/ibm-developer-skills-network/vselh-ci-cd-final-project-template).
2. Click the green **"Use this template"** button -> **"Create a new repository"**.
3. Select your GitHub account and name the repository: `ci-cd-final-project`.
4. Ensure the visibility is set to **Public** so that it can be accessed by the AI Grader and peers.
5. Click **Create repository from template**.

> [!WARNING]
> Do not fork the repository. Use the **Use this template** option to avoid conflicts when making pull requests or during automated grading.

### 2. Initialize Development Environment (Cloud IDE)

Because the Cloud IDE is ephemeral, you must run these initialization steps every time you restart your lab environment:

```bash
# 1. Export your GitHub username
export GITHUB_ACCOUNT={your_github_username}

# 2. Clone your repository
git clone https://github.com/$GITHUB_ACCOUNT/ci-cd-final-project.git

# 3. Navigate to the project directory and run setup
cd ci-cd-final-project
bash ./bin/setup.sh

# 4. Close the current terminal to activate the new environment
exit
```

### 3. Validate Environment

Open a new terminal (**Terminal -> New Terminal**) and run the following commands to verify that the Python Virtual Environment is active:

```bash
which python
# The output should point to the local virtual environment (.venv) path

python --version
# Should output Python 3.8.x or 3.9.x
```

---

## Part 1: CI Pipeline with GitHub Actions

In this section, you will write the CI workflow inside `.github/workflows/workflow.yml`.

### Exercise 1: Create Basic Workflow

Define a workflow that triggers on `push` and `pull_request` events targeting the `main` branch.

### Exercise 2: Add Linting Step (Flake8)

Use the `flake8` module to check syntax quality and conform to coding standards.

### Exercise 3: Add Unit Testing Step (Nose Tests)

Use `nosetests` to run automated unit tests on the backend microservice.

### Complete Configuration: `.github/workflows/workflow.yml`

Below is the complete configuration combining Exercises 1, 2, and 3:

```yaml
name: CI workflow

on:
    push:
        branches: ['main']
    pull_request:
        branches: ['main']

jobs:
    build:
        runs-on: ubuntu-latest
        container:
            image: python:3.9-slim

        steps:
            - name: Checkout
              uses: actions/checkout@v3

            - name: Install dependencies
              run: |
                  python -m pip install --upgrade pip
                  pip install -r requirements.txt

            - name: Lint with flake8
              run: |
                  flake8 service --count --select=E9,F63,F7,F82 --show-source --statistics
                  flake8 service --count --max-complexity=10 --max-line-length=127 --statistics

            - name: Run unit tests with nose
              run: |
                  nosetests -v --with-spec --spec-color --with-coverage --cover-package=app
```

---

### Exercise 4: Validate GitHub Actions Workflow

1. Save your changes, commit, and push them to GitHub:
    ```bash
    git add .github/workflows/workflow.yml
    git commit -m "feat: add CI workflow with linting and unit tests"
    git push origin main
    ```
2. Navigate to the **Actions** tab in your GitHub repository and verify that the workflow runs successfully (green checkmark).
3. You can also verify this from the terminal using GitHub CLI (`gh`):
    ```bash
    gh run list
    gh run view <run-id> --verbose
    ```

---

## Part 2: CD Pipeline with Tekton and OpenShift

In this section, you will create continuous delivery steps using **Tekton Tasks** and orchestrate them in **OpenShift Pipelines**.

### Exercise 5: Create Tekton Cleanup Task

This task prepares the workspace by removing existing output files before the clone process starts.

### Exercise 6: Create Tekton Test Task (Nose)

This task runs the unit tests inside the OpenShift cluster environment before building the container image.

### Complete Configuration: `.tekton/tasks.yml`

Save both tasks in `.tekton/tasks.yml` using `---` as a separator:

```yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
    name: cleanup
spec:
    workspaces:
        - name: source
    steps:
        - name: remove
          image: alpine:3
          env:
              - name: WORKSPACE_SOURCE_PATH
                value: $(workspaces.source.path)
          workingDir: $(workspaces.source.path)
          securityContext:
              runAsNonRoot: false
              runAsUser: 0
          script: |
              #!/usr/bin/env sh
              set -eu
              echo "Removing all files from ${WORKSPACE_SOURCE_PATH} ..."
              if [ -d "${WORKSPACE_SOURCE_PATH}" ] ; then
                rm -rf "${WORKSPACE_SOURCE_PATH:?}"/*
                rm -rf "${WORKSPACE_SOURCE_PATH}"/.[!.]*
                rm -rf "${WORKSPACE_SOURCE_PATH}"/..?*
              fi

---
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
    name: nose
spec:
    workspaces:
        - name: source
    params:
        - name: args
          description: Arguments to pass to nose
          type: string
          default: '-v'
    steps:
        - name: nosetests
          image: python:3.9-slim
          workingDir: $(workspaces.source.path)
          script: |
              #!/bin/bash
              set -e
              python -m pip install --upgrade pip wheel
              pip install -r requirements.txt
              nosetests $(params.args)
```

---

### Exercise 7: Create OpenShift Pipeline and Deployment

#### Step 1: Install Tekton Tasks in Your Cluster

Run the following command in your terminal to apply the custom tasks to your OpenShift cluster:

```bash
kubectl apply -f .tekton/tasks.yml
```

#### Step 2: Create a Persistent Volume Claim (PVC)

Create a PVC with these parameters using either the console or terminal:

- **StorageClass**: `skills-network-learner`
- **PVC Name**: `oc-lab-pvc`
- **Size**: `1GB`

#### Step 3: Create and Configure Your Pipeline

Create a pipeline called `oc-pipelines-oc-final` and link it to a workspace named `output` pointing to your PVC `oc-lab-pvc`.

Arrange the steps in this specific order:

1. **cleanup** (uses your custom `cleanup` task)
2. **git-clone** (clones your repository into the workspace)
3. **flake8-linting** (runs syntax validation)
4. **nose-tests** (uses your custom `nose` task)
5. **buildah** (builds your container image using the Dockerfile)
6. **openshift-client / deploy** (deploys the service using the following command):
    ```bash
    oc create deployment $(params.app-name) --image=$(params.build-image) --dry-run=client -o yaml | oc apply -f -
    ```

---

## Submission and Evaluation Documentation

> [!IMPORTANT]
> Coursera provides **two submission options**. You only need to choose one. We highly recommend **Option 1** for instant grading via Coursera AI Grader.

### Option 1: AI-Graded Submission (20 Points)

For Option 1, you will be redirected to the Coursera AI grading utility, where you will provide public links and upload your evidence files.

| Assignment Task | Item to Submit                                                                                          | Points |
| :-------------- | :------------------------------------------------------------------------------------------------------ | :----: |
| **Task 1**      | Public GitHub URL of `README.md` containing the project name.                                           |   2    |
| **Task 2**      | GitHub URL of `.github/workflows/workflow.yml` (showing linting and test steps).                        |   4    |
| **Task 3**      | GitHub URL of `.tekton/tasks.yml` (showing custom `cleanup` and `nose` tasks).                          |   4    |
| **Task 4**      | OpenShift PVC details screenshot named **`oc-pipelines-console-pvc-details.png`**.                      |   2    |
| **Task 5**      | Terminal log file or screenshot named **`cicd-github-validate`** showing successful GitHub Actions run. |   2    |
| **Task 6**      | Visual pipeline structure screenshot named **`oc-pipelines-oc-final.png`**.                             |   2    |
| **Task 7**      | Successful green pipeline execution screenshot named **`oc-pipelines-oc-green.png`**.                   |   2    |
| **Task 8**      | Application execution log named **`oc-pipelines-app-logs`**.                                            |   2    |
| **Total**       | **Passing Score: 14 Points (70%)**                                                                      | **20** |

---

### Option 2: Peer-Graded Submission (20 Points)

For Option 2, upload your files in the "My Submission" section for peer review.

- **Task 1**: Public URL of your repository (`https://github.com/{username}/ci-cd-final-project.git`).
- **Task 2**: GitHub URL of `.github/workflows/workflow.yml` for linting.
- **Task 3**: GitHub URL of `.github/workflows/workflow.yml` for testing.
- **Task 4**: GitHub URL of `.tekton/tasks.yml` for the cleanup task.
- **Task 5**: GitHub URL of `.tekton/tasks.yml` for the nose test task.
- **Task 6**: Screenshot of OpenShift PVC details (**`oc-pipelines-console-pvc-details.png`**).
- **Task 7**: Screenshot of successful GitHub Actions run (**`cicd-github-validate.png`**).
- **Task 8**: Screenshot of the OpenShift pipeline structure (**`oc-pipelines-oc-final.png`**).
- **Task 9**: Screenshot of successful green pipeline execution (**`oc-pipelines-oc-green.png`**).
- **Task 10**: Screenshot of application console execution logs (**`oc-pipelines-app-logs.png`**).

---

### Evidence File Naming Guidelines

To ensure automated tools and peers can successfully evaluate your work, name your evidence files exactly as specified below:

1. **`oc-pipelines-console-pvc-details.png`** (OpenShift Console PVC page)
2. **`cicd-github-validate.png`** or **`cicd-github-validate.txt`** (GitHub Actions validation logs)
3. **`oc-pipelines-oc-final.png`** (Visual OpenShift Pipeline representation)
4. **`oc-pipelines-oc-green.png`** (Completed green pipeline run)
5. **`oc-pipelines-app-logs.png`** or **`oc-pipelines-app-logs.txt`** (Running application log details)

---

## Authors

- **IBM Skills Network** (Original Template & Scenario)
- **Alima Akhter** (Contributor)
- **Alfaturachman** (As the practitioner completing this assignment)

---

_© IBM Corporation 2024. All rights reserved._
