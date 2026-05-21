# Power Platform ALM Pipeline - CI/CD Automation

## 📋 Overview

This GitHub Actions workflow automates the **Application Lifecycle Management (ALM)** process for Power Platform solutions, providing a complete CI/CD pipeline from Development to Target environments.

### What This Workflow Does

1. **Exports** solutions from DEV environment
2. **Versions** the solution automatically (major/minor/patch)
3. **Commits** unpacked solution files to Git for source control
4. **Deploys** to TARGET environment with **Update** or **Upgrade** strategy
5. **Tags** releases in Git for tracking


---

## ⚙️ Features

### ✅ Automated Solution Versioning
- **Patch** (1.0.0.0 → 1.0.0.1): Bug fixes
- **Minor** (1.0.0.0 → 1.1.0.0): New features
- **Major** (1.0.0.0 → 2.0.0.0): Breaking changes

### ✅ Source Control Integration
- Unpacked solution files stored in Git
- Easy to track changes with diff
- Git tags for each release (e.g., `v1.0.0.1-SolutionName`)

### ✅ Flexible Deployment Strategies

#### **Update Mode** (Safe - Default)
- Adds new components
- Updates existing components
- **Does NOT remove deleted components**
- Best for: Regular updates, bug fixes

#### **Upgrade Mode** (Full Replacement)
- Adds new components
- Updates existing components
- **Removes deleted components from target**
- Best for: Major releases, cleanup

### ✅ Managed Solution Deployment
- TARGET environment receives **Managed** solutions
- Prevents accidental modifications in production
- Follows ALM best practices

---

## 🚀 Setup Instructions

### Step 1: Create Required Secrets

Go to **Settings → Secrets and variables → Actions** and add:

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `DEV_URL` | DEV environment URL | `https://orgname-dev.crm.dynamics.com` |
| `TARGET_URL` | TARGET environment URL | `https://orgname-prod.crm.dynamics.com` |
| `CLIENT_ID` | Azure App Registration Client ID | `12345678-1234-1234-1234-123456789abc` |
| `CLIENT_SECRET` | Azure App Registration Secret | `your-secret-value` |
| `TENANT_ID` | Azure Tenant ID | `87654321-4321-4321-4321-cba987654321` |

### Step 2: Azure App Registration Setup

1. **Create App Registration** in Azure Portal
2. **Grant API Permissions:**
   - Dynamics CRM → `user_impersonation`
3. **Add Application Users** to both DEV and TARGET environments with:
   - System Administrator role (or appropriate security role)

### Step 3: Repository Structure

Ensure your repository has this structure:

your-repo/
├── .github/
│   └── workflows/
│       └── deploy-solution.yml    ← This workflow file
├── solutions/
│   ├── zip_files/                 ← Zip files (gitignored)
│   ├── YourSolution1/             ← Unpacked files (tracked in Git)
│   └── YourSolution2/             ← Unpacked files (tracked in Git)
└── .gitignore

---

## 📖 Usage Guide

### Running the Workflow

1. Go to **Actions** tab in GitHub
2. Select **"Export and Deploy Solution with Versioning"**
3. Click **"Run workflow"**
4. Fill in the parameters:

| Parameter | Description | Options |
|-----------|-------------|---------|
| **Solution Name** | Name of the solution in Power Platform | e.g., `TestSolution` |
| **Version Bump** | How to increment version | `patch`, `minor`, `major` |
| **Import Strategy** | How to deploy to TARGET | `update`, `upgrade` |
| **Release Notes** | What changed in this version | Free text |

### Example Scenarios

#### Scenario 1: Bug Fix Release
Solution Name: CustomerPortal
Version Bump: patch
Import Strategy: update
Release Notes: Fixed login issue on mobile devices
**Result:** 1.0.0.5 → 1.0.0.6 (Update mode - safe)

#### Scenario 2: New Feature Release
Solution Name: CustomerPortal
Version Bump: minor
Import Strategy: update
Release Notes: Added new dashboard for customer analytics
**Result:** 1.0.0.6 → 1.1.0.0 (Update mode - safe)

#### Scenario 3: Major Release with Cleanup
Solution Name: CustomerPortal
Version Bump: major
Import Strategy: upgrade
Release Notes: Redesigned UI, removed deprecated forms
**Result:** 1.1.0.0 → 2.0.0.0 (Upgrade mode - removes old components)

---

## 🔄 Workflow Steps Explained

### Job 1: Export and Version

```yaml
1. Checkout Code
   ↓
2. Setup Power Platform CLI
   ↓
3. Export Solution from DEV (Managed)
   ↓
4. Unpack Solution to Folder
   ↓
5. Read & Update Version in Solution.xml
   ↓
6. Commit Changes to Git
   ↓
7. Create Git Tag (e.g., v1.0.0.1-SolutionName)
```

### Job 2: Deploy to Target

```yaml
1. Checkout Latest Code from Git
   ↓
2. Setup Power Platform CLI
   ↓
3. Pack Solution as Managed
   ↓
4a. IF import_strategy = 'update':
    → Direct Import (Update Mode)
   
4b. IF import_strategy = 'upgrade':
    → Import as Holding Solution
    → Apply Upgrade (Removes deleted components)
   ↓
5. Display Deployment Summary
```

---

## 📊 Update vs Upgrade - When to Use What?

| Scenario | Strategy | Reason |
|----------|----------|--------|
| Bug fixes | **Update** | Safe, no component removal |
| New features | **Update** | Incremental addition |
| Monthly releases | **Update** | Regular maintenance |
| Deprecated feature removal | **Upgrade** | Clean up old components |
| Major version change | **Upgrade** | Full replacement |
| Annual cleanup | **Upgrade** | Remove technical debt |

---

## 🛠️ Troubleshooting

### Error: "Solution is already installed as unmanaged"

**Cause:** Target environment has unmanaged solution, you're deploying managed.

**Fix:** Manually delete the solution from Target environment first, then re-run.

---

### Error: "Cannot find required file Customizations.xml"

**Cause:** Trying to pack Managed export as Unmanaged or vice versa.

**Fix:** Ensure export is Managed and unpack type matches.

---

### Error: "Holding solution and target must both be managed"

**Cause:** Using Upgrade strategy with Unmanaged solution.

**Fix:** Upgrade only works with Managed solutions. Ensure `solution-type: "Managed"` in pack step.

---

### Unpacked Files Not Pushing to Git

**Cause:** `git add .` might be pushing zip files instead of folders.

**Fix:** 
- Add `solutions/zip_files/` to `.gitignore`
- Use `git add solutions/` in commit step

---

## 📁 File Structure in Git

After successful run, your Git will have:
solutions/
├── YourSolution/
│   ├── CanvasApps/
│   ├── Entities/
│   ├── OptionSets/
│   ├── Workflows/
│   ├── WebResources/
│   └── Other/
│       └── Solution.xml    ← Version updated here
└── zip_files/              ← Ignored by Git

---

## 🏷️ Git Tags

Each successful deployment creates a Git tag:
v1.0.0.1-SolutionName
v1.1.0.0-SolutionName
v2.0.0.0-SolutionName

View all tags: `git tag -l`

---

## 📈 Best Practices

### ✅ DO:
- Use **Update** for regular releases (safer)
- Use **Upgrade** for major cleanup (quarterly/annually)
- Test in UAT before production
- Write meaningful release notes
- Keep DEV as Unmanaged (development flexibility)
- Deploy Managed to TARGET (production safety)

### ❌ DON'T:
- Use Upgrade for every release (too destructive)
- Skip release notes (important for audit)
- Deploy Unmanaged to production
- Manually modify files in Git (always export from DEV)

---

## 🔐 Security Notes

- Service Principal has System Administrator access
- Secrets are stored encrypted in GitHub
- Never commit secrets to Git
- Regularly rotate Client Secrets (Azure App Registration)

---

## 📞 Support & Contributions

For issues or improvements:
1. Create an issue in this repository
2. Submit a pull request with improvements
3. Contact the DevOps team

---

## 📜 License

This workflow is maintained by Hashir Khan for internal ALM automation.

---

## 🎓 Additional Resources

- [Power Platform ALM Guide](https://docs.microsoft.com/power-platform/alm/)
- [GitHub Actions Documentation](https://docs.github.com/actions)
- [Power Platform CLI Reference](https://docs.microsoft.com/powerapps/developer/data-platform/powerapps-cli)

---

**Last Updated:** 20 May 2026  
**Maintained By:** Hashir Khan
