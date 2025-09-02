# Cherry Pick Optimize

A GitHub Actions workflow system for optimizing cherry-pick operations between branches with automated tag management.

## Overview

This repository provides automated workflows to:
- **Auto-tag commits** on main branch based on REG ticket patterns
- **Cherry-pick missing tags** from main to staging branches
- **Compare tags** between branches to identify missing releases

## Prequiste

### Required Secrets
- `PERSONAL_ACCESS_TOKEN` - GitHub personal access token with appropriate permissions

## Features

### 🏷️ Auto-Tagging (`git-tag-on-main.yml`)
Automatically creates versioned tags when commits are pushed to the main branch:
- Detects REG-xxxx patterns in commit messages
- Generates incremental tags (e.g., REG-8879.001, REG-8879.002)
- Pushes tags to the repository

### 🍒 Cherry-Pick Missing Tags (`cherry-pick-tags.yml`)
Intelligently cherry-picks commits with specific tag prefixes:
- **Manual workflow dispatch** with configurable parameters
- Finds missing tags between source and target branches
- Processes tags in version order (small to large)
- Handles merge conflicts gracefully with conflict markers
- Creates pull requests with detailed summaries

**Parameters:**
- `tag_prefix`: Tag prefix to search for (e.g., REG-1234)
- `target_branch`: Branch to cherry-pick into (default: staging)
- `source_branch`: Branch to cherry-pick from (default: main)

### 📊 Tag Comparison (`tag-comparison.yml`)
Compares tags between staging and main branches:
- Lists unique tags in each branch
- Exports results as artifacts
- Provides GitHub Actions summary reports

## Usage

### Tag Comparison Workflow (First Step)

**Before cherry-picking, compare tags between branches to identify what needs to be cherry-picked:**

1. Navigate to **Actions** → **Compare Tags Between Staging and Main**
2. Click **Run workflow**
3. Review the results to identify:
   - Tags in main but not in staging (candidates for cherry-picking)
   - Tags in staging but not in main (already cherry-picked)

The comparison provides:
- GitHub Actions summary with formatted results
- Downloadable artifacts with detailed tag differences
- REG-xxxx prefix grouping for easy identification

### Cherry-Pick Workflow (Second Step)

**After identifying missing tags from comparison, cherry-pick specific tag prefixes:**

1. Navigate to **Actions** → **Cherry Pick Missing Tags**
2. Click **Run workflow**
3. Configure parameters:
   - **Tag prefix**: Enter REG ticket number (e.g., `REG-8879`)
   - **Target branch**: Select destination branch (default: staging)
   - **Source branch**: Select source branch (default: main)
4. Click **Run workflow**

The workflow will:
- Find all tags with the specified prefix in the source branch
- Identify which tags are missing from the target branch
- Cherry-pick missing commits in version order
- Handle conflicts by creating commits with conflict markers
- Generate a pull request with detailed results

### Conflict Resolution

When conflicts occur, the workflow:
1. Creates commits with conflict markers for manual resolution
2. Includes detailed instructions in the pull request
3. Provides commands to find and resolve conflicts:

```bash
# Find conflicted files
grep -r "<<<<<<< HEAD" .
grep -r "=======" .
grep -r ">>>>>>>" .
```

**Resolution steps:**
1. Checkout the PR branch locally
2. Find commits with "CONFLICTS" in the message
3. Resolve conflict markers in affected files
4. Stage resolved files: `git add <file>`
5. Amend the conflict commit: `git commit --amend`
6. Push changes: `git push --force-with-lease`

## Workflow Permissions

The workflows require these permissions:
- `contents: write` - For creating tags and commits
- `pull-requests: write` - For creating pull requests


### Git Configuration
The workflows automatically configure Git with:
- User: `github-actions[bot]`
- Email: `github-actions[bot]@users.noreply.github.com`

## Tag Format

Tags follow the pattern: `REG-{number}.{version}`
- REG number extracted from commit messages
- Version is a 3-digit incremental number (001, 002, 003...)

**Example:** `REG-8879.001`, `REG-8879.002`

## Branch Strategy

- **main**: Primary development branch with auto-tagging
- **staging**: Release preparation branch for cherry-picked features
- **cherry-pick branches**: Temporary branches created for cherry-pick pull requests

## Workflow Results

### Cherry-Pick Summary
Each cherry-pick operation provides:
- ✅ **Successful**: Clean cherry-picks without conflicts  
- ⚠️ **With conflicts**: Commits requiring manual resolution
- ❌ **Failed**: Cherry-picks that couldn't be processed

### Pull Request Details
Generated pull requests include:
- Configuration summary (prefix, source/target branches)
- Detailed results breakdown
- Lists of processed tags by status
- Conflict resolution instructions
- Commands for manual intervention

## Example Scenarios

### Scenario 1: Clean Cherry-Pick
```
Tag prefix: REG-8879
Missing tags: REG-8879.001, REG-8879.002
Result: Both tags cherry-picked successfully ✅
```

### Scenario 2: With Conflicts  
```
Tag prefix: REG-8879
Missing tags: REG-8879.001, REG-8879.002, REG-8879.003
Results:
- REG-8879.001: ✅ Success
- REG-8879.002: ⚠️ Conflicts (manual resolution needed)
- REG-8879.003: ✅ Success
```

## Contributing

This workflow system is designed for internal release management. Modifications should consider:
- Tag naming conventions
- Branch protection rules
- Merge conflict handling strategies
- Pull request review processes
