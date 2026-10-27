
# GitHub Lifecycle Automation Demo with Ansible & AAP

A demonstration suite showing how to automate the end-to-end GitHub repository lifecycle inside **Ansible Automation Platform (AAP)** using Ansible playbooks, `ansible.scm`, and the GitHub REST API.

---

## Overview

This project provides an automated CI/CD and repository management workflow designed to run within AAP Execution Environments (EEs). It handles everything from repository initialization and code management to pull request automation and eventual cleanup without requiring external heavy Python dependencies (such as `PyGithub`).

---

## Workflow Playbooks

| Playbook | Purpose |
| --- | --- |
| `01_setup_github_repo.yml` | Provisions the GitHub repository, initializes the default branch, and establishes the local workspace. |
| `02_manage_pr_workflow.yml` | Retrieves the repository with `ansible.scm.git_retrieve`, creates a feature branch, applies changes, publishes via `ansible.scm.git_publish`, and opens a Pull Request. |
| `03_cleanup_repo.yml` | Deletes temporary branches, cleans the workspace, and manages repository teardown. |

---

## Prerequisites & Requirements

### Collections (`collections/requirements.yml`)

```yaml
---
collections:
  - name: community.general
    version: ">=8.0.0"
  - name: ansible.scm

```

### Authentication & AAP Setup

The playbooks authenticate against GitHub via a Custom Credential Type injecting the following environment variables:

* **`GITHUB_TOKEN`**: A GitHub Personal Access Token (PAT) with `repo` and admin permissions. Marked as `secret: true`.
* **`GITHUB_USER`**: The associated GitHub username or `x-access-token`.

---

## Key Design Patterns

* **Zero-Dependency REST Calls:** Uses `ansible.builtin.uri` for GitHub API operations (PRs, settings) to avoid `ModuleNotFoundError: PyGithub` inside standard containerized Execution Environments.
* **`ansible.scm` Integration:** Employs `ansible.scm.git_retrieve` and `ansible.scm.git_publish` for declarative repository checkouts and pushes.
* **Credential Masking:** Enforces `no_log: true` on all authentication tasks to prevent token exposure in AAP standard output logs.