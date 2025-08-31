# 🛠 Windows Update Errors — Complete Step-by-Step Fix Guide (PowerShell)

**Fix Windows Update errors fast with copy-paste PowerShell commands and plain-English steps.**  
This guide covers **Windows 10 & Windows 11** issues like: updates stuck at 0%/100%, **failed cumulative updates**, **download problems**, and error codes **0x800f0831, 0x80070002, 0x80073712, 0x8024a203, 0x800f0922**.  
It’s designed for beginners — **follow the steps from top to bottom** and run each block **as Administrator**.

**SEO / keywords (for discoverability):** Windows Update error fix, Windows 11 update problems, Windows update stuck, PowerShell update repair, DISM RestoreHealth, SFC /scannow, reset Windows Update components, fix error 0x800f0831, 0x80070002, 0x80073712, 0x8024a203, 0x800f0922, Microsoft Store not updating, update fails, KB install error, Windows update troubleshooting guide, how to repair Windows image, catroot2, SoftwareDistribution, network reset, WSReset, manual KB install, in-place repair upgrade.

---

## 📋 How to use this guide
- **Go in order** (Step 0 → Step 10).  
- **Run commands in PowerShell as Administrator.**  
- After big steps, **restart** your PC and then open **Settings → Windows Update → Check for updates**.  
- If a step fixes your issue, you can stop there.

---

## 🧰 Step 0 — Open PowerShell **as Administrator** (3 easy ways)

**Way A (Search):**  
1. Press **Win** key, type **powershell**.  
2. Right-click **Windows PowerShell** → **Run as administrator** → **Yes**.

**Way B (Win+X menu):**  
1. Press **Win + X**.  
2. Click **Windows PowerShell (Admin)** or **Windows Terminal (Admin)**.  
3. If Terminal opens, make sure the tab is **PowerShell** (not Command Prompt).

**Way C (Start menu list):**  
1. Start → **Windows PowerShell** folder → **Windows PowerShell** (right-click) → **More** → **Run as administrator**.

> You’ll know it’s elevated if the window title says **Administrator: Windows PowerShell**.

---

## ✅ Step 1 — Check & repair system files (SFC)
Copy the block and wait for it to finish (10–30 minutes). If errors are found, **restart** before Step 2.
```powershell
sfc /scannow

# 🧱 Step 2 — Repair the Windows image (DISM)

Run all three commands; each can take several minutes.

```DISM /Online /Cleanup-Image /CheckHealth
```DISM /Online /Cleanup-Image /ScanHealth
```DISM /Online /Cleanup-Image /RestoreHealth

# 🔄 Step 3 — Safe reset of Windows Update components (keeps backups)

This resets services and renames caches.
```net stop wuauserv
```net stop bits
```net stop cryptsvc
```net stop msiserver

```ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
```ren C:\Windows\System32\catroot2 catroot2.old

```net start msiserver
```net start cryptsvc
```net start bits
```net start wuauserv

# 🧹 Step 4 — Aggressive cache clear (only if Step 3 didn’t help)

This permanently deletes caches. Restart after running.

```net stop wuauserv
```net stop bits
```net stop cryptsvc
```net stop msiserver

```rmdir /s /q C:\Windows\SoftwareDistribution
```rmdir /s /q C:\Windows\System32\catroot2

```net start msiserver
```net start cryptsvc
```net start bits
```net start wuauserv

# 🌐 Step 5 — Reset the network stack (fixes download/Store issues)

Run these, then restart the PC.

```netsh winsock reset
```netsh int ip reset
```ipconfig /flushdns
```ipconfig /release
```ipconfig /renew

# 🛒 Step 6 — Fix Microsoft Store (only if Store updates also fail)

First, soft-reset; if needed, re-register the Store for all users.

```wsreset.exe
```Get-AppxPackage -AllUsers Microsoft.WindowsStore | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppxManifest.xml"}

# 🧽 Step 7 — Clean up component store (optional: space & stability)

Useful on systems with long update history or low free space.
```DISM /Online /Cleanup-Image /StartComponentCleanup /ResetBase

# 📦 Step 8 — Manually install a specific KB (when one update keeps failing)

1) Download the related .msu from the Microsoft Update Catalog.

2) Replace the path below and run:

```wusa.exe C:\Path\To\KBxxxxxxx.msu /quiet /norestart

# 📄 Step 9 — Generate WindowsUpdate.log (for help & diagnostics)

Creates a readable log on your Desktop.
```Get-WindowsUpdateLog -LogPath "$env:USERPROFILE\Desktop\WindowsUpdate.log"

# 🛠 Step 10 — In-place repair upgrade (last resort, keeps apps/files)
1) Mount a Windows 10/11 ISO.
2) Run setup.exe → choose Keep personal files and apps.
3) Finish setup → check Windows Update again.

🎯 Quick fixes by error code (use alongside Steps 1–5)

0x800f0831 — missing payload / prerequisite
```DISM /Online /Cleanup-Image /RestoreHealth
```DISM /Online /Cleanup-Image /RestoreHealth /Source:D:\sources\install.wim /LimitAccess

0x80070002 / 0x80070003 — files not found / bad cache

```net stop wuauserv
```net stop bits
```ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
```net start bits
```net start wuauserv

0x80073712 — corrupted system files
```sfc /scannow
```DISM /Online /Cleanup-Image /RestoreHealth

0x8024a203 — Windows Update service trouble
```Restart-Service wuauserv
```Restart-Service bits

0x800f0922 — .NET or system reserved partition issue
```DISM /Online /Enable-Feature /FeatureName:NetFx3 /All
```DISM /Online /Enable-Feature /FeatureName:NetFx3 /All /Source:D:\sources\sxs /LimitAccess

🔎 Handy checks (optional)

Windows version/build (GUI):
```winver

Windows version/build (PowerShell):
```Get-ComputerInfo | Select-Object OsName,OsVersion,OsBuildNumber

Service status:

```Get-Service wuauserv,bits,cryptsvc,msiserver


🧭 Troubleshooting ladder (what to try next)

Updates stuck or looping → Steps 1–4
Store + Update both failing → Step 5 + Step 6
Low space / very old install → Step 7
One specific KB keeps failing → Step 8
Still broken → Step 10 (repair upgrade)

🤝 Contributing

PRs welcome — add new fixes, expand error-specific sections, or attach anonymized logs.










