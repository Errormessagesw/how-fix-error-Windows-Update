# 🛠 Windows Update Errors — Step-by-Step Fix Guide (PowerShell)

This README gives you one **clear, copy-paste, step-by-step** procedure to fix **Windows Update** problems on **Windows 10/11**.  
Follow the steps **from top to bottom**. After each major step, **restart** your PC if told to do so, then go to **Settings → Windows Update → Check for updates**.

> **Run everything in PowerShell as Administrator**: Start → type “powershell” → right-click → **Run as administrator**.

---

## TL;DR (Run these in order, one block at a time)
1. **SFC** (system file check)  
2. **DISM** (repair Windows image)  
3. **Safe reset** of Windows Update components  
4. **Aggressive cache clear** (only if #3 didn’t help)  
5. **Network reset** (helps with download/Store issues)  
6. **Microsoft Store fixes** (only if Store updates fail)  
7. **Component cleanup** (optional space/stability)  
8. **Manual KB install** (if a specific update keeps failing)  
9. **Generate Update log** (for diagnostics)  
10. **In-place repair upgrade** (last resort, keeps your apps/files)

---

## Step 1 — Check & repair system files (SFC)
Copy the block and wait for it to finish (can take 10–30 minutes). If errors are found, **restart** before moving on.
```powershell
sfc /scannow```
