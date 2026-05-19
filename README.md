# D365 CRM ALM - Simple Implementation

## 🎯 What This Does
- Exports solution from DEV environment
- Unpacks and commits to GitHub
- Packs and imports to TARGET environment

## 📋 Setup Checklist

### ✅ Step 1: App Registration
- [ ] Created app registration in Azure Portal
- [ ] Noted Client ID
- [ ] Noted Tenant ID
- [ ] Created Client Secret
- [ ] Added Dynamics CRM API permission
- [ ] Granted admin consent

### ✅ Step 2: Application Users
- [ ] Created application user in DEV environment (hashirsalestrial)
- [ ] Created application user in TARGET environment (hashirnewtrial)
- [ ] Both have System Administrator role

### ✅ Step 3: GitHub Secrets
- [ ] DEV_URL added
- [ ] TARGET_URL added
- [ ] CLIENT_ID added
- [ ] CLIENT_SECRET added
- [ ] TENANT_ID added

## 🚀 How to Use

### 1. Create a Simple Solution in DEV
1. Go to https://hashirsalestrial.crm4.dynamics.com
2. Settings → Solutions → New Solution
3. Name: **TestSolution** (simple naam rakho)
4. Publisher: Create new or use existing
5. Version: 1.0.0.1
6. Add any component (ek custom field hi dal do test ke liye)

### 2. Run the Workflow
1. GitHub repository → Actions tab
2. Click "Export and Deploy Solution"
3. Click "Run workflow"
4. Enter:
   - Solution name: **TestSolution**
   - Version: **1.0.0.1**
5. Click "Run workflow" button

### 3. Check Results
- Workflow run hogi (5-10 minutes)
- Solutions folder mein unpacked files aayengi
- TARGET environment mein solution import ho jayega

## 📂 Repository Structure
```
d365-alm-simple/
├── .github/
│   └── workflows/
│       └── export-and-deploy.yml    ← Workflow file
├── solutions/
│   └── TestSolution/                ← Unpacked solution (auto-generated)
└── README.md
```

## 🐛 Troubleshooting

### Error: "Application user not found"
- Check application user created hai dono environments mein
- System Administrator role assigned hai

### Error: "Invalid client secret"
- Secret refresh karke new banao
- GitHub secret update karo

### Error: "Solution not found"
- Solution naam exactly same ho (case-sensitive)
- Solution DEV environment mein exist karta hai

## 📚 Next Steps (Future)
Once this works:
- Multiple solutions
- Branching strategy
- Versioning
- Validation rules
- Rollback capability

## 🆘 Need Help?
Check workflow logs:
- Actions tab → Click on workflow run → Click on failed step
