# Team Git Workflow & Contribution Guide

To prevent merge conflicts and avoid breaking `main`, our team follows a strict **Feature-Branch & Pull Request (PR)** workflow.

---

## 1. Golden Rules (Read This First)

1. **NEVER commit directly to `main`:** The `main` branch is protected and reserved strictly for tested, working code.
2. **One Task = One Branch:** Never work on two different components in the same branch.
3. **Always Pull Before You Branch:** Keep your local `main` synchronized with GitHub.
4. **No Floats For Money:** All amounts are integer cents ($100.00 = 100000); all percentages are basis points (1% = 100 bps).
5. **No Secrets in Git:** Never commit `.env` or API keys. Verify `git status` before committing.
6. **Update `AI_DISCLOSURE.md`:** If you used Copilot, Gemini, or Claude to write or refactor code, log it immediately.

---

## 2. Standard Branch Naming Convention

Always prefix your branch name with your area:

- `metta/<feature-name>` (e.g., `metta/fractional-splits`, `metta/reserve-floor`)
- `agent/<feature-name>` (e.g., `agent/bridge-logic`, `agent/override-handler`)
- `app/<feature-name>` (e.g., `app/streamlit-dashboard`, `app/audit-view`)
- `docs/<feature-name>` (e.g., `docs/readme-hfas`, `docs/video-script`)
- `tests/<feature-name>` (e.g., `tests/scenarios-json`)

---

## 3. Step-by-Step Daily Workflow

### Step 1: Update Your Local Main

Before starting any new work, make sure your local copy has the latest approved code:

```powershell
git checkout main
git pull origin main
```

### Step 2: Create a Fresh Working Branch

Before starting any new work, make sure your local copy has the latest approved code:

```powershell
git checkout -b <branch-prefix>/<feature-name>
# Example: git checkout -b metta/fractional-splits
```

### Step 3: Work & Run Tests Locally

Do your edits. Before committing, run tests to verify nothing broke:

```powershell
pytest
```

### Step 4: Commit with Conventional Messages

Check which files changed:

```powershell
git status
```

Status and commit:

```powershell
git add <specific-files>
git commit -m "<type>: <concise description of what changed>"
```

Allowed `<type>` prefixes:

- `feat:` (New feature or rule)
- `fix:` (Bug fix or calculation correction)
- `test:` (Adding or updating test fixtures)
- `docs:` (Documentation or README updates)
- `chore:` (Environment, dependencies, or settings)

_Example:_

```powershell
git commit -m "feat: implement basis points fractional calculation in metta"

```

### Step 5: Push Branch to GitHub

```powershell
git push -u origin <your-branch-name>

```

### Step 6: Open a Pull Request (PR)

1. Go to the repository on GitHub.
2. Click the **"Compare & pull request"** banner.
3. **Title:** Provide a clear summary of the change.
4. **Description:** Mention which scenario/issue it addresses, confirm `pytest` passed locally, and verify zero-float math.
5. Request a review from the Team Lead.
6. Once approved and GitHub Actions checks pass, merge into `main` using **Squash and Merge**.

---

## 4. How to Handle Merge Conflicts (If They Happen)

If GitHub indicates your branch has conflicts with `main`:

```powershell
# 1. Fetch latest changes
git checkout main
git pull origin main

# 2. Switch back to your working branch
git checkout <your-branch-name>

# 3. Merge main into your branch
git merge main

```

4. Open VS Code. Look for files highlighted with red markers.
5. Use the inline buttons (`Accept Current Change`, `Accept Incoming Change`, or `Accept Both Changes`) to resolve the conflict.
6. Run `pytest` to confirm everything still passes.
7. Stage the resolved files and push:

```powershell
git add .
git commit -m "chore: resolve merge conflicts with main"
git push origin <your-branch-name>

```

---

## 5. First-Time Teammate Setup (Cloning)

Send this exact block to each teammate joining the repo:

```powershell
# Clone the repository
git clone https://github.com/<your-username>/fractional-approval-agent.git
cd fractional-approval-agent

# Create and activate virtual environment
python -m venv .venv
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
.\.venv\Scripts\Activate.ps1

# Install pinned dependencies
python -m pip install --upgrade pip
pip install -r requirements.txt

# Run initial test suite to verify installation
pytest

```
