# Team Git Workflow and Contribution Guide

To avoid merge conflicts and a broken `main`, we use a **feature-branch and pull request (PR)** workflow. Our history and docs are also evidence of how the team worked, so commit small, push often and write things down.

---

## 1. Golden Rules (read this first)

1. **Never commit directly to `main`.** It holds tested, working code only.
2. **One task = one branch.** Do not mix components in a branch.
3. **Pull `main` before you branch.**
4. **No floats for money.** Amounts are integer minor units ($100.00 = 10000 cents). Percentages are basis points (1% = 100 bps, 100% = 10000 bps).
5. **No secrets in Git.** Never commit `.env` or API keys. Check `git status` before every commit.
6. **Log AI use immediately in `AI_DISCLOSURE.md`** (Copilot, Gemini, Claude, Antigravity or any other tool).
7. **Never edit a test to make it pass.** Changes to test files need the lead's approval.
8. **Nobody merges their own PR.**
9. **If it is not pushed, it did not happen.** Push at least once an hour while working.
10. If CI is red on `main`, fixing it is everyone's first job.

---

## 2. Branch naming

Prefix with your area, then the task ID from the plan, then a short name:

- `metta/<task-id>-<name>` (for example `metta/1.3-settlement-rule`)
- `agent/<task-id>-<name>` (for example `agent/2.2-bridge`, `agent/4.2-override`)
- `app/<task-id>-<name>` (for example `app/6.2-settle-page`)
- `docs/<task-id>-<name>` (for example `docs/0.6-market-research`)
- `tests/<task-id>-<name>` (for example `tests/1.8-oracle`)

Keep branches short-lived (about 4 hours of work). Merge often.

---

## 3. Daily workflow

### Step 1: Update your local main

```powershell
git checkout main
git pull origin main
```

### Step 2: Create a fresh working branch

```powershell
git checkout -b <branch-prefix>/<task-id>-<name>
# Example: git checkout -b metta/1.3-settlement-rule
```

### Step 3: Work and test locally

Run the Python tests, and the MeTTa CLI tests if you touched MeTTa:

```powershell
pytest
metta metta/tests/test_royalties.metta
```

### Step 4: Commit with conventional messages

```powershell
git status
git add <specific-files>
git commit -m "<type>(<scope>): <what changed> [<task-id>]"
```

Types: `feat`, `fix`, `test`, `docs`, `chore`, `refactor`. Scopes: `metta`, `bridge`, `audit`, `override`, `parser`, `ui`, `ci`, `docs`.

Example:

```powershell
git commit -m "feat(metta): add settlement rule with conservation check [1.3]"
```

- For MeTTa changes, paste the CLI output into the commit body.
- For AI-assisted commits, add a last line to the message: `Assisted-by: <tool and model>`.

### Step 5: Push your branch

```powershell
git push -u origin <your-branch-name>
```

### Step 6: Open a pull request

1. On GitHub, click **Compare & pull request**.
2. **Title:** a clear summary. **Description:** use the PR template, link the issue ("Closes #12"), confirm tests passed and zero-float maths.
3. Request a review. Review pairs: Role B's rules are reviewed by A; A's bridge and override by B; C's parser and UI by D; D's tests by A. The lead approves changes to tests, contracts, `docs/CONTEXT.md` and `AGENTS.md`.
4. When approved and CI is green, merge with **Create a merge commit** or **Rebase and merge**. **Do not squash**, so every teammate's commits stay visible in the history.
5. Delete the branch after merging.

---

## 4. Handling merge conflicts

```powershell
# 1. Fetch latest changes
git checkout main
git pull origin main

# 2. Switch back to your branch
git checkout <your-branch-name>

# 3. Merge main into your branch
git merge main
```

4. Open VS Code and look for files marked with conflicts.
5. Use the inline buttons (Accept Current, Accept Incoming, Accept Both) and read the result carefully. For `metta/` and `tests/` files, ask the owner before choosing.
6. Run `pytest` (and the MeTTa CLI tests if relevant).
7. Stage the resolved files and push:

```powershell
git add <resolved-files>
git commit -m "chore: resolve merge conflicts with main"
git push origin <your-branch-name>
```

---

## 5. First-time teammate setup

```powershell
git clone https://github.com/<your-username>/fractional-approval-agent.git
cd fractional-approval-agent

python -m venv .venv
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
.\.venv\Scripts\Activate.ps1

python -m pip install --upgrade pip
pip install -r requirements.txt

# Create your own .env from the example (never commit it)
Copy-Item .env.example .env

pytest
```

Then read, in this order: `AGENTS.md`, `docs/CONTEXT.md`, this file, `PROGRESS.md`.

---

## 6. Documentation as you go

| File | Update when | Who |
|---|---|---|
| `PROGRESS.md` | After every task: what changed, evidence, decisions, AI used | Task owner |
| `docs/DECISIONS.md` | Any choice that changes scope or design | Lead |
| `docs/OPEN_QUESTIONS.md` | A question is asked or answered | Whoever asked |
| `AI_DISCLOSURE.md` | Any AI-assisted work | Task owner |
| `README.md` | End of each phase | Docs owner |
| `SUBMISSION.md` | Final day, against the official rubric | Lead |

Keep screenshots and CLI output in `docs/evidence/`.

---

## 7. Phase gates and tags

When a phase passes its gate, the lead tags `main` and writes a short GitHub Release note:

| Gate | Tag | Passes when |
|---|---|---|
| 1 | `gate-1-symbolic-core` | MeTTa CLI tests pass with no Python maths |
| 2 | `gate-2-bridge` | Bridge and audit tests pass; CLI and Python results match |
| 3 | `gate-3-override` | Override tests pass; conservation holds after an override |
| 4 | `gate-4-ui` | UI shows raw atoms; every number comes from the proof |
| 5 | `v1.0-submission` | Fresh clone passes CI; video under 180 seconds; rubric checked |

---

## 8. Repo admin (lead, once)

1. Make the first commit directly on `main` with the scaffolding (see the repo layout in `README.md`), **then** turn on branch protection.
2. Protect `main`: require a pull request, 1 approval, passing CI, code-owner review, no force pushes. On a free GitHub plan these rules may not be available for private repos. If so, make the repo public or follow the rules by convention.
3. Create an issue per task ID and a milestone per phase.
4. Replace the placeholder handles in `.github/CODEOWNERS`.
5. Share the repo with BASIX.MARKET as the event requires.

---

## 9. Keeping the repo clean

- Do not commit empty placeholder files. Create a file when it has real content.
- One progress log: `PROGRESS.md`. Do not keep a second one.
- No scratch work in the repo. Keep experiments (such as the Omega checkout) outside it or in the git-ignored `scratch/`.
- No absolute local paths or `file:///` links in committed files. Use relative links.
- No keys anywhere, including `.env.example`, logs, screenshots and `docs/evidence/`. If a key was ever pasted into a file or chat, rotate it.
- Runtime output (`audit_trail.json`) is git-ignored. Commit a small sample under `docs/evidence/` instead.
- Check `git status` and `git diff --stat` before every commit. Delete leftover debug code and commented-out blocks.
- Pin dependencies (`hyperon==<tested version>`). Review every change to `requirements.txt`.
- Before submitting, clone into a fresh folder and run the install and tests from scratch.
