---
Title: Windows10 store not launching
Published: 2020-03-17 00:20:00 +0000
Tags: 
  - Windows10
  - Troubleshoot
---
Some days ago, I can't exactly precise when, the Windows store on my laptop suddenly stop launching. 
At that time, I checked all Windows and manufacturer updates and confirmed everything was already installed. After restarting my laptop (works 99.9% of the times as you might know) the next step would be to search the web.

The most common fix I found recommended to run wsreset following these steps:
1. Tab on Win+R to open Run Command;
2. Type wsreset.exe in the dialog box;
3. A prompt will open for a while. Wait for it to close;
4. Windows Store will automatically open.

Wonderful! Couldn't be simpler... wait!

![ms-windows-store:PurgeCaches error dialog](/images/ms_win_store_purge_cache_error.png)

Currently updating? That's weird. I'm pretty sure I'm not installing anything, and I restarted my laptop a couple of minutes ago. Time to hit the web again...

After many link clicks, failed attempts trying to run commands with outdated solutions and so on... I finally found a solution which mainly consists in re-registering the Windows Store app manifest with PowerShell.

We first need to find the location of the AppxManifest.xml file that is in the Windows Store installation path. Then we run the [Add-AppxPackage](https://docs.microsoft.com/en-us/powershell/module/appx/add-appxpackage?view=win10-ps) cmdlet with some flags.

```powershell
$manifestPath = (Get-AppxPackage Microsoft.WindowsStore).InstallLocation + '\AppxManifest.xml';
Add-AppxPackage -DisableDevelopmentMode -Register $manifest
```

And now the Windows Store finally opens and I can go back to install my favorite WSL distro so I can integrate Docker Desktop with WSL2. But that's another story.
