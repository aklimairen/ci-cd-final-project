# CI/CD Final Project — IBM Coursera

[![CI Workflow](https://img.shields.io/github/actions/workflow/status/YOUR_USERNAME/ci-cd-final-project/workflow.yml?branch=main&label=CI%20Build&logo=github)](https://github.com/YOUR_USERNAME/ci-cd-final-project/actions)
[![Python](https://img.shields.io/badge/Python-3.9-blue?logo=python)](https://www.python.org/)
[![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?logo=docker)](https://www.docker.com/)
[![Tekton](https://img.shields.io/badge/Tekton-Pipelines-FD495C?logo=tekton)](https://tekton.dev/)
[![OpenShift](https://img.shields.io/badge/OpenShift-Ready-EE0000?logo=redhatopenshift)](https://www.redhat.com/en/technologies/cloud-computing/openshift)

> **Course:** [CI/CD Tools and Practices](https://www.coursera.org/learn/continuous-integration-and-continuous-delivery-ci-cd) — IBM / Coursera

Capstone project demonstrating a full CI/CD pipeline around a Python/Flask microservice, using **GitHub Actions** for continuous integration and **Tekton Pipelines** on **OpenShift** for continuous delivery.

---

## CI/CD Pipeline

```
Push / Pull Request
        │
        ▼
┌──────────────────────────────────────┐
│        GitHub Actions (CI)           │
│                                      │
│  1. Checkout code                    │
│  2. Install dependencies             │
│  3. Lint with Flake8                 │
│  4. Run unit tests + coverage report │
└────────────────┬─────────────────────┘
                 │ on success
                 ▼
┌──────────────────────────────────────┐
│      Tekton Pipelines (CD)           │
│           on OpenShift               │
│                                      │
│  Task 1: cleanup  — wipe workspace   │
│  Task 2: nose     — test in-cluster  │
└──────────────────────────────────────┘
```

### GitHub Actions — `.github/workflows/workflow.yml`

Triggers on every push and pull request to `main`. Runs inside a `python:3.9-slim` container for reproducible builds.

| Step     | Tool                    | Purpose                                        |
| -------- | ----------------------- | ---------------------------------------------- |
| Checkout | `actions/checkout@v3` | Pull source code                               |
| Install  | pip                     | Resolve dependencies from `requirements.txt` |
| Lint     | Flake8                  | Catch syntax errors and enforce code style     |
| Test     | Nosetests + Coverage    | Run unit tests and report coverage             |

### Tekton Pipelines — `.tekton/tasks.yml`

Kubernetes-native tasks designed to run on Red Hat OpenShift.

| Task        | Image               | What it does                                       |
| ----------- | ------------------- | -------------------------------------------------- |
| `cleanup` | `alpine:3`        | Safely wipes the shared workspace before each run  |
| `nose`    | `python:3.9-slim` | Installs deps and runs the test suite inside a pod |

Both tasks use Tekton **workspaces** (shared PersistentVolumeClaims) and the `nose` task accepts a **params** argument for flexible test configuration.

---

## What I Practised

* Writing GitHub Actions workflows with multi-step jobs running in containerized environments
* Configuring Flake8 for both hard-fail (syntax errors) and soft-warn (style) passes
* Writing Tekton `Task` definitions using the `tekton.dev/v1beta1` API
* Understanding Tekton's workspace model for sharing state between pipeline tasks
* Containerizing a Python app with Docker following security best practices (non-root user, slim base image, layer caching)

---

## Project Structure

```
├── .github/workflows/workflow.yml   # GitHub Actions CI
├── .tekton/tasks.yml                # Tekton Tasks for OpenShift
├── Dockerfile                       # Container definition
├── service/                         # Flask microservice
├── tests/                           # Unit test suite
└── requirements.txt                 # Dependencies
```

---

## Planned Improvements

* Add a Tekton `Pipeline` YAML to chain tasks end-to-end with a `PipelineRun`
* Migrate from deprecated `nosetests` to `pytest`
* Add a security scanning step (`bandit`) to the GitHub Actions workflow
* Add OpenShift deployment manifests (`Deployment`, `Service`, `Route`)


## Learning behind this project

This project is the capstone for a CI/CD course I took. The notes and concepts I worked through while building this are documented separately in my learning repo:

→ [cicd-learning-notes](https://github.com/aklimairen/cicd-learning-notes) — covers CI/CD basics, GitHub Actions, Tekton, and OpenShift
