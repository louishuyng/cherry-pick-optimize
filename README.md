# Cherry Pick Optimize

A GitHub Actions workflow system for optimizing cherry-pick operations between branches with automated tag management.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Features](#features)
- [Usage](#usage)
- [Conflict Resolution](#conflict-resolution)
- [Contributing](#contributing)

## Overview

Automated workflows for REG ticket-based release management:
- **Auto-tag commits** on main branch based on REG ticket patterns
- **Cherry-pick missing tags** from main to staging branches
- **Compare tags** between branches to identify missing releases

## Prerequisites

### Required Secrets
- `PERSONAL_ACCESS_TOKEN` - GitHub personal access token with appropriate permissions

## Features

### 🏷️ Auto-Tagging
[**Workflow: git-tag-on-main.yml**](.github/workflows/git-tag-on-main.yml)

Automatically creates versioned tags when commits are pushed to main:
- Detects REG-xxxx patterns in commit messages
- Generates incremental tags (e.g., REG-8879.001, REG-8879.002)
- Pushes tags to the repository

### 🍒 Cherry-Pick Missing Tags
[**Workflow: cherry-pick-tags.yml**](.github/workflows/cherry-pick-tags.yml)

Intelligently cherry-picks commits with specific tag prefixes:
- Manual workflow dispatch with configurable parameters
- Finds missing tags between source and target branches
- Processes tags in version order
- Handles merge conflicts gracefully
- Creates pull requests with detailed summaries

**Parameters:**
- `tag_prefix`: Tag prefix to search for (e.g., REG-1234)
- `target_branch`: Branch to cherry-pick into (default: staging)
- `source_branch`: Branch to cherry-pick from (default: main)

### 📊 Tag Comparison
[**Workflow: tag-comparison.yml**](.github/workflows/tag-comparison.yml)

Compares tags between staging and main branches:
- Lists unique tags in each branch
- Exports results as artifacts
- Provides GitHub Actions summary reports

## Usage

### Step 1: Compare Tags
1. Navigate to **Actions** → **Compare Tags Between Staging and Main**
2. Click **Run workflow** to identify missing tags

### Step 2: Cherry-Pick Tags
1. Navigate to **Actions** → **Cherry Pick Missing Tags**
2. Configure parameters:
   - **Tag prefix**: REG ticket number (e.g., `REG-8879`)
   - **Target branch**: Destination branch (default: staging)
   - **Source branch**: Source branch (default: main)
3. Click **Run workflow**

## Conflict Resolution

When conflicts occur during cherry-picking:
1. The workflow creates commits with conflict markers
2. A pull request is generated with resolution instructions
3. Manually resolve conflicts and amend the commits

**Quick resolution:**
```bash
# Find conflicted files
grep -r "<<<<<<< HEAD" .

# After resolving conflicts:
git add <file>
git commit --amend
git push --force-with-lease
```

## Tag Format

Tags follow the pattern: `REG-{number}.{version}`
- Example: `REG-8879.001`, `REG-8879.002`

## Contributing

This workflow system is designed for internal release management. Modifications should consider:
- Tag naming conventions
- Branch protection rules
- Merge conflict handling strategies
- Pull request review processes
