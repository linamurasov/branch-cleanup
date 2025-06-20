# GitHub Branch Cleanup Automation

This repository contains a GitHub Actions workflow designed to automatically detect and delete stale feature branches that meet the following criteria:

- Already merged into the `main` branch
- No recent activity: Last commit was made more than **1 month ago**

The goal is to maintain a clean and manageable Git repository by automating the process of removing feature branches that have already been incorporated into the main codebase. This is particularly useful in development workflows based on GitHub Flow where many short-lived branches are created and merged regularly (like dev, feature-*, etc.). Reduces manual cleanup effort.

---

## Current Branch Status

| Branch Name | Status      | Merged to `main`? | Last Commit Older Than 1 Month? |
|-------------|-------------|-------------------|----------------------------------|
| `feature-1` | Merged      | 🟢Yes               | Yes                              |
| `feature-2` | Merged      | 🟢Yes               | Yes                              |
| `feature-3` | In Progress | 🔴No                | N/A                              |

Only `feature-1` and `feature-2` are eligible for deletion. `feature-3` will remain until it is merged and becomes old enough to delete.

---

## Workflow Overview

### Trigger Configuration

The workflow can be run manually or scheduled via cron.

```yaml
on:
  workflow_dispatch:
  schedule:
    - cron: '0 1 * * 0'  # Every Sunday at 1:00 AM UTC
```

### Key Steps in the Workflow
1) Checkout full repository history using actions/checkout@v3 with fetch-depth: 0
2) Fetch all remote branches to ensure up-to-date visibility into the repo
3) List merged and unmerged remote branches compared to origin/main
4) Filter for merged branches only
5) Determine the age of the last commit on each merged branch
6) Delete the branch from origin if:
  - It is merged into origin/main
  - The last commit is older than 30 days (2592000 seconds)

## Available Workflows

### 1. Branch Deletion Workflow

- **Runner:** `ubuntu-latest`
- **Triggers:** `workflow_dispatch`, scheduled every **Sunday at 1:00 AM UTC**
- **Deletion Rule:** Deletes remote branches that:
  - Are merged into `main`
  - Have a last commit older than **1 month** (2592000 seconds)

### 2. Branch Deletion Workflow (Testing)

- **Runner:** `ubuntu-latest`
- **Triggers:** `workflow_dispatch`, scheduled every 5 minutes (**schedule is currently commented out**)
- **Deletion Rule:** Deletes remote branches that:
  - Are merged into `main`
  - Have a last commit older than **30 seconds**, but younger than **3000 seconds**

### 3. Branch Deletion Workflow (Testing on Windows)

- **Runner:** `windows-latest`
- **Triggers:** `workflow_dispatch`, scheduled every 5 minutes (**schedule is currently commented out**)
- **Deletion Rule:** Deletes remote branches that:
  - Are merged into `main`
  - Have a last commit older than **20 seconds**, but younger than **3000 seconds**

### Testing Instructions

You can use workflows **2** or **3** to test the branch deletion logic:

1. Create a **new feature branch**
2. Make a commit and **push it to the remote**
3. Open a pull request and **merge it into `main`**
4. Manually trigger **"Branch Deletion Workflow (Testing)"** from the Actions tab
5. Confirm the branch is deleted after the configured time threshold

These workflows help validate the logic without waiting for actual aging over days or weeks.

---
