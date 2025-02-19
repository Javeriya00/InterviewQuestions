# **GitHub Enterprise Repository Migration Documentation**

## **1️⃣ Understanding the Difference Between Different Migrations**
Migrating a repository from **one GitHub Enterprise Organization to another** is different from migrating a repository from **a personal GitHub account to another personal account** due to organizational controls, permissions, security measures, and automation settings.

### **Key Differences:**
| Feature | GitHub Enterprise Org to Org | Personal GitHub to Another Personal GitHub |
|---------|-----------------------------|---------------------------------------------|
| **Repository Transfer** | Possible via UI-based transfer or manual code migration | Simple push from one GitHub account to another |
| **Secrets Handling** | Secrets are NOT transferred | Secrets are NOT transferred |
| **GitHub Actions Workflows** | Need to verify actions, secrets, and permissions | Workflows remain but require secrets to be reconfigured |
| **Commit History** | Retained in both approaches | Retained in both approaches |
| **Self-Hosted Runners** | Need to be manually reconfigured | Not applicable unless self-hosted runners are used |
| **Permissions Required** | Admin access to both orgs | Write access to both repos |

---

## **2️⃣ UI-Based Workflow for Repository Migration**
### **Steps to Transfer a Repository Using UI:**
1. **Go to Source Organization Repo:**
   - Navigate to the repository in the **old GitHub Enterprise Organization**.
   
2. **Initiate Transfer:**
   - Click **Settings → Danger Zone → Transfer Repository**.
   - Enter the new organization name (`new-org-name/repo-name`).
   - Confirm the transfer.

3. **Post-Transfer Steps:**
   - The repository **is removed from the old organization** and appears in the new organization.
   - **Secrets are NOT transferred** and must be re-added manually.
   - **Self-hosted runners must be reconfigured** if applicable.

### **Important Considerations for UI-Based Transfer:**
```diff
❌ Does the old repository remain? No, it is moved completely.
❌ Are GitHub Actions secrets transferred? No, they must be manually re-added.
❌ Are workflow run logs transferred? No, old workflow logs are not available.
✅ Is commit history retained? Yes, commit history remains intact.
```

---

## **3️⃣ Code-Based Workflow for Repository Migration**
### **Steps to Migrate Using Git Commands:**
```sh
# Clone the Old Repository Locally
 git clone --bare https://github.com/old-org/repo-name.git
 cd repo-name.git
```
```sh
# Push the Repository to the New Organization
 git push --mirror https://github.com/new-org/repo-name.git
```

3. **Set Up GitHub Actions and Secrets Again:**
   - **Re-add repository secrets manually** (Settings → Secrets and Variables → Actions).
   - Verify and update GitHub Actions YAML files in `.github/workflows/`.
   - If using **self-hosted runners**, reconfigure them in the new organization.

### **Where to Run the Commands?**
- Run on your **local machine**.
- You need **Admin or Write Access** to both repositories.

### **Permissions Required for Code-Based Migration:**
| Task | Required Permission |
|------|---------------------|
| Clone the repository | Read access to the old repository |
| Push to new organization | Write/Admin access to new repository |
| Transfer repository (UI method) | Admin access in both organizations |

---

## **4️⃣ Handling GitHub Actions, Secrets, and Runners**
### **Secrets Handling**
- **Secrets do NOT get transferred.**
- Go to **Settings → Secrets and Variables → Actions** in the new repo and manually re-add them.
- If using **organization-level secrets**, ensure they are available in the new organization.

### **Discovering Personal Access Tokens (PAT) Used in the Code**
- **Look for hardcoded tokens** in the repository files.
- Check the **GitHub Actions workflows (`.github/workflows/*.yml`)** for any references to environment variables.
- Use **GitHub Secret Scanning** or search for `GITHUB_TOKEN`, `PAT`, `ACCESS_TOKEN` in the codebase.

### **GitHub Runners Considerations**
- **Self-Hosted Runners:**
  - If self-hosted runners were used, they must be **re-registered in the new organization**.
  - Navigate to **Settings → Actions → Runners** to set them up again.
- **GitHub-Hosted Runners:**
  - No manual configuration needed unless there are specific restrictions in place.

---

## **5️⃣ Best Practices for GitHub Enterprise Repository Migration**
1. **Backup the Old Repository First:**
   - Use `git clone --bare` to ensure you have a copy of the repository.

2. **Migrate Using Code-Based Approach (Recommended):**
   - Allows easy rollback if something goes wrong.
   - Ensures that a copy of the old repository remains accessible.

3. **Document All Secrets and Configurations Before Migration:**
   - Extract the list of existing secrets (names only, values are not visible).
   - Ensure GitHub Actions and environment variables are well-documented.

4. **Verify GitHub Actions and Runners in the New Organization:**
   - Manually re-add secrets.
   - Check if **self-hosted runners need re-registration**.
   - Run test workflows to confirm everything is working.

---

## **6️⃣ Testing the Migration Process Before the Real Migration**
### **Can I Get GitHub Enterprise for Free?**
- **Yes, GitHub Enterprise offers a 30-day free trial** (for business accounts).
- Sign up here: [GitHub Enterprise Trial](https://github.com/enterprise/trial).
- If you want to test, use **GitHub Free or Pro accounts** for a mock migration.

### **How to Test Migration Without Enterprise?**
1. **Create a personal repository** on GitHub.
2. **Push it to another personal account** using the same migration steps.
3. **Test workflows, secrets, and permissions in the new repo.**
4. **If organization-level testing is needed, use GitHub Team Plan ($4/month).**

---

## **7️⃣ Alternative Methods for GitHub Enterprise Migration**
- **GitHub API-Based Migration:** Use GitHub REST API or GraphQL for automation.
- **GitHub Enterprise Importer (Beta):** GitHub provides an official **repository migration tool** for enterprise customers.
- **Third-Party Migration Tools:** Tools like `ghe-migrator` for large-scale GitHub Enterprise migrations.

---

## **8️⃣ Final Recommendations**
- **Use the code-based approach** for a safer migration.
- **Manually verify secrets and GitHub Actions after migration.**
- **Test the migration first in a personal repository before handling an enterprise migration.**
- **Document everything, including secrets, runners, and permissions, before starting the migration.**

This document covers all aspects of GitHub Enterprise repository migration. Let me know if you need any additional details! 🚀

