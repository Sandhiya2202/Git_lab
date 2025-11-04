# Learn Git & GitHub Actions

A concise, hands‑on guide to get productive with **Git** and **GitHub Actions**. Use this README as your mini‑playbook and checklist while practicing.

---

## 📦 Prereqs
- Git installed: `git --version`
- GitHub account
- (Optional) VS Code + Git extension

---

## 🧭 Git Mental Model (TL;DR)
- **Working tree** → your files on disk  
- **Index (staging area)** → what will go into the next commit  
- **Local repo** → your commits (branches, tags)  
- **Remote** → GitHub copy of your repo

```mermaid
flowchart LR
  WT[Working Tree] -- git add --> IDX[Index / Staging]
  IDX -- git commit --> LR[Local Repo]
  LR -- git push --> REM[Remote (GitHub)]
  REM -- git fetch/pull --> LR
```

---

## 🚀 Quick Start
```bash
# 1) Create a repo
mkdir git-actions-lab && cd git-actions-lab
git init

# 2) First file + commit
echo "# Git & Actions Lab" > README.md
git add README.md
git commit -m "chore: initial commit"

# 3) Link to GitHub (replace URL)
git remote add origin https://github.com/<you>/git-actions-lab.git
git branch -M main
git push -u origin main
```

---

## 🧰 Everyday Git Commands
```bash
git status                 # what's changed
git diff                   # unstaged changes
git add <file>             # stage file
git add -p                 # stage interactively (hunks)
git commit -m "message"    # commit staged changes
git log --oneline --graph  # compact history
git restore <file>         # discard unstaged changes
git restore --staged <file># unstage
```

### Branching & Merging
```bash
git checkout -b feature/calc
# ...edit files...
git add -A && git commit -m "feat: add calc"
git checkout main
git merge feature/calc     # or use PR on GitHub
```

### Sync with Remote
```bash
git fetch --all
git pull --rebase          # keep history linear (optional)
git push
```

### Tags & Releases
```bash
git tag v1.0.0
git push origin v1.0.0
```

### Ignore Junk
Create `.gitignore`:
```
__pycache__/
*.pyc
.DS_Store
.env
.venv/
```
```bash
git add .gitignore
git commit -m "chore: add .gitignore"
```

---

## 🧠 Helpful Aliases (optional)
```bash
git config --global alias.s "status -sb"
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.co checkout
git config --global alias.cm "commit -m"
git config --global alias.stash-pop "stash pop"
```

---

## 🩹 Troubleshooting Snippets
```bash
# Undo last commit but keep changes staged
git reset --soft HEAD~1

# Undo last commit and unstage, keep changes in files
git reset --mixed HEAD~1

# Hard reset to remote main (DANGER: discards local changes)
git fetch
git reset --hard origin/main
```

---

## 🧪 Practice Checklist (Git)
- [ ] Create repo & first commit
- [ ] Make a feature branch and commit twice
- [ ] Open a Pull Request on GitHub
- [ ] Merge PR into `main`
- [ ] Create a tag `v0.1.0`
- [ ] Resolve a simple merge conflict
- [ ] Add a `.gitignore` and test it with `.DS_Store`

---

# ⚙️ GitHub Actions (CI/CD)

**GitHub Actions** runs workflows (YAML files) in `.github/workflows/` on a hosted runner.  
Key concepts: **workflow → jobs → steps**. Jobs run in **parallel** by default; steps run **sequentially** inside a job.

- Triggers: `push`, `pull_request`, `workflow_dispatch` (manual), `schedule` (CRON)
- Runners: `ubuntu-latest`, `windows-latest`, `macos-latest`, or self-hosted
- Reusable pieces: **actions** from the Marketplace (`actions/checkout`, `actions/setup-python`, etc.)

---

## 🧪 Example 1: Python CI
Save as `.github/workflows/python-ci.yml`:
```yaml
name: Python CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install deps
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt || true

      - name: Run tests
        run: |
          pytest -q || echo "No tests yet"
```

Add a **status badge** to your README:
```md
![Python CI](https://github.com/<you>/git-actions-lab/actions/workflows/python-ci.yml/badge.svg)
```

---

## 🧪 Example 2: Node CI with Cache + Matrix
`.github/workflows/node-ci.yml`:
```yaml
name: Node CI

on:
  pull_request:
  push:
    branches: [ main ]

jobs:
  build:
    strategy:
      matrix:
        node: [18, 20, 22]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
          cache: "npm"
      - run: npm ci
      - run: npm test --silent
```

---

## 🔐 Secrets, Permissions, Artifacts
- Add secrets in **Repo Settings → Secrets and variables → Actions** (`MY_API_KEY`).  
  Use with `${{ secrets.MY_API_KEY }}`.
- Limit token power (good practice):
```yaml
permissions:
  contents: read
  pull-requests: write
```
- Upload build artifacts:
```yaml
- uses: actions/upload-artifact@v4
  with:
    name: dist
    path: dist/**
```

---

## 🔁 Reusable Workflows
Create `.github/workflows/reuse-test.yml`:
```yaml
name: Reuse Example
on: workflow_call

jobs:
  echo:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Reusable workflow says hi!"
```
Call it from another workflow:
```yaml
jobs:
  call-reuse:
    uses: <you>/<repo>/.github/workflows/reuse-test.yml@main
```

---

## 🗓️ Scheduled (CRON) & Manual Triggers
```yaml
on:
  schedule:
    - cron: "0 9 * * 1"   # Mondays 09:00 UTC
  workflow_dispatch:       # manual button in Actions tab
```

---

## 🧪 Practice Checklist (Actions)
- [ ] Create `.github/workflows/python-ci.yml`
- [ ] Trigger it with a push → watch logs in **Actions** tab
- [ ] Add a badge to README
- [ ] Add a matrix build job (Node or Python versions)
- [ ] Upload an artifact (e.g., `dist/` or test report)
- [ ] Use a repository secret in a step
- [ ] Add `workflow_dispatch` and run manually

---

## 🧹 Optional: Enforce Checks on PRs
In **Settings → Branches → Branch protection rules**:
- Require status checks to pass before merging
- Require PR reviews
- Dismiss stale approvals on new commits

---

## 📚 Further Reading
- Git Book: https://git-scm.com/book/en/v2
- Pro Git Tips: `git help` + `man git-<command>`
- GitHub Actions docs: https://docs.github.com/actions

---

## ✅ Next Steps
1. Create this repo and paste the sample workflows.
2. Make a trivial code change, open a PR, and watch CI run.
3. Add a badge and a scheduled workflow.
4. Iterate: linting, tests, coverage, build & deploy.

Happy shipping! 🚀
