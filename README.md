# 🛑 Release Freeze Control

[![CI/CD Pipeline](https://img.shields.io/badge/CI%2FCD%20Pipeline-passing-brightgreen?cacheSeconds=60&timestamp=20260304)]()
[![Freeze Dispatcher](https://img.shields.io/badge/Freeze%20Dispatcher-passing-brightgreen?cacheSeconds=60&timestamp=20260304)]()
![GitHub Repo stars](https://img.shields.io/github/stars/vikas-wanchoo-devops/release-freeze-control?style=social&cacheSeconds=60&timestamp=20260304)
[![License](https://img.shields.io/badge/license-Apache--2.0-green?cacheSeconds=60&timestamp=20260304)]()


---

## 📌 Overview
This repository acts as the **central governance system** for code freeze enforcement across multiple downstream repositories.  
By updating a single flag in the central workflow, you can block or unblock merges in all connected repos automatically.

---

## ⚙️ How It Works
1. **Central Control**  
   - The central workflow (`notify-freeze.yml`) defines whether merges are blocked or allowed.  
   - A flag is toggled in the workflow to enforce freeze across all downstream repos.

2. **Notify Workflow**  
   - `.github/workflows/notify-freeze.yml` runs whenever the freeze state changes.  
   - It dispatches a `freeze-updated` event to downstream repos.  
   - Uses a loop over a repo list so you can scale to many repos without duplicating code.

3. **Downstream Enforcement**  
   - Each downstream repo has a `code-freeze.yml` workflow listening for `repository_dispatch`.  
   - If freeze is active, PRs are blocked with ❌.  
   - If freeze is inactive, PRs show ✅ and merges are allowed.

4. **PR Re-checks**  
   - The central workflow re‑requests PR check suites across all downstream repos.  
   - Ensures open PRs immediately reflect the updated freeze state.

---

## 🚀 Setup
### Central Repo (`release-freeze-control`)
- Add a **Personal Access Token (PAT)** with `repo` + `workflow` scopes.  
- Save it as a secret named `PAT_TOKEN`.  
- Ensure `.github/workflows/notify-freeze.yml` is present.  
- Update the `repos=(...)` array in the workflow to include all repos you want governed.  

### Downstream Repos
- Add `.github/workflows/code-freeze.yml` with:
  ```yaml
  on:
    repository_dispatch:
      types: [freeze-updated]
    pull_request:
      types: [opened, synchronize, reopened, ready_for_review]
