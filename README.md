# 🛑 Release Freeze Control

[![CI/CD Pipeline](https://img.shields.io/badge/CI%2FCD%20Pipeline-passing-brightgreen)]()
[![Code Freeze Check](https://img.shields.io/badge/Code%20Freeze%20Check-passing-brightgreen)]()
[![Docker Pulls](https://img.shields.io/badge/docker%20pulls-1.8k-blue)]()
[![Stars](https://img.shields.io/badge/stars-1-white)]()
[![License](https://img.shields.io/badge/license-Apache--2.0-green)]()

---

## 📌 Overview
This repository acts as the **central governance system** for code freeze enforcement across multiple downstream repositories.  
By updating a single JSON file (`code-freeze-config/freeze.json`), you can block or unblock merges in all connected repos automatically.

---

## ⚙️ How It Works
1. **Central Flag**  
   - `freeze.json` contains:
     ```json
     {
       "freeze_active": true,
       "reason": "Quarterly release window"
     }
     ```
   - Flip `freeze_active` between `true` (block merges) and `false` (allow merges).

2. **Notify Workflow**  
   - `.github/workflows/notify-freeze.yml` runs whenever `freeze.json` changes.
   - It dispatches a `freeze-updated` event to downstream repos.

3. **Downstream Enforcement**  
   - Each downstream repo has a `code-freeze.yml` workflow listening for `repository_dispatch`.
   - If `freeze_active` is `true`, PRs are blocked with ❌.  
   - If `freeze_active` is `false`, PRs show ✅ and merges are allowed.

---

## 🚀 Setup
### Central Repo (`release-freeze-control`)
- Add a **Personal Access Token (PAT)** with `repo` + `workflow` scopes.
- Save it as a secret named `PAT_TOKEN`.
- Ensure `.github/workflows/notify-freeze.yml` is present.

### Downstream Repos
- Add `.github/workflows/code-freeze.yml` with:
  ```yaml
  on:
    repository_dispatch:
      types: [freeze-updated]
    pull_request:
      types: [opened, synchronize, reopened, ready_for_review]
