# 🛠 Windows Update Errors – Fix & Troubleshooting with PowerShell

This repository provides **clear, detailed, copy-paste fixes** for common **Windows Update** problems on **Windows 10/11**.  
If updates get stuck, fail to install, or show error codes like **0x800f0831, 0x80070002, 0x8024a203, 0x80073712, 0x800f0922**, use the steps below.

---

## ✅ How to use this guide
1. Open **PowerShell as Administrator** (Start → type “powershell” → **Run as administrator**)  
2. Copy the blocks **exactly** as shown (one block at a time)  
3. Reboot when told, then try Windows Update again  

---

## 📌 Quick fixes (run top to bottom)

### 1) Check and repair system files
<pre> ```powershell sfc /scannow DISM /Online /Cleanup-Image /RestoreHealth Restart-Service wuauserv ``` </pre>

2) Repair the Windows component store (DISM)
   
<pre> ```DISM /Online /Cleanup-Image /CheckHealth
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth
``` </pre>

Optional (if /RestoreHealth fails with sources, mount an ISO and replace D:):
<pre> ```DISM /Online /Cleanup-Image /RestoreHealth /Source:D:\sources\install.wim /LimitAccess``` </pre>

3) Safe reset of Windows Update components

<pre> ``` net stop wuauserv
net stop bits
net stop cryptsvc
net stop msiserver

ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
ren C:\Windows\System32\catroot2 catroot2.old

net start msiserver
net start cryptsvc
net start bits
net start wuauserv``` </pre>


4) Aggressive cache clear (use only if step 3 didn’t help)

<pre> ```net stop wuauserv
net stop bits
net stop cryptsvc
net stop msiserver

rmdir /s /q C:\Windows\SoftwareDistribution
rmdir /s /q C:\Windows\System32\catroot2

net start msiserver
net start cryptsvc
net start bits
net start wuauserv``` </pre>

5) Reset networking (helps with download/Store failures)
<pre> ```netsh winsock reset
netsh int ip reset
ipconfig /flushdns
ipconfig /release
ipconfig /renew``` </pre>

6) Clean up component store (free space / remove superseded)
<pre> ```DISM /Online /Cleanup-Image /StartComponentCleanup /ResetBase``` </pre>

7) Re-register update services
<pre> ```sc.exe config wuauserv start= auto
sc.exe config bits start= delayed-auto
sc.exe config cryptsvc start= auto
sc.exe config msiserver start= demand``` </pre>

8) Generate WindowsUpdate.log for diagnostics
<pre> ```Get-WindowsUpdateLog -LogPath "$env:USERPROFILE\Desktop\WindowsUpdate.log"``` </pre>

🎯 Fix by error code (copy only what you need)
Error 0x800f0831 – missing payload / prerequisite

<pre> ```DISM /Online /Cleanup-Image /RestoreHealth
DISM /Online /Cleanup-Image /RestoreHealth /Source:D:\sources\install.wim /LimitAccess``` </pre>

Error 0x80070002 / 0x80070003 – files not found
<pre> ```net stop wuauserv
net stop bits
ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
net start bits
net start wuauserv``` </pre>


Error 0x80073712 – corrupted system files

sfc /scannow
DISM /Online /Cleanup-Image /RestoreHealth















