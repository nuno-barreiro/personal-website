---
Title: Cleaning up ASP.NET temp files with PowerShell
Published: 2017-11-14 11:55:00 +0000
Tags: 
  - ASP.NET
  - IIS
  - PowerShell
  - Scripting
---
One of the things that you must certainly have to deal with when developing ASP.NET applications in when you try to debug some piece of code but for some reason it seems that it keeps loading old versions of the assemblies and not the most recent build you just did.

This is certainly frustrating and one of the most common reasons for it to happen is due to the temporary ASP.NET files that are stored on your machine. Cleaning those is not hard at all and the following PowerShell script will do that for you.

``` powershell
Get-ChildItem "C:\Windows\Microsoft.NET\Framework\v*\Temporary ASP.NET Files" -Recurse | Remove-Item -Recurse
Get-ChildItem "$Env:Temp\Temporary ASP.NET Files" -Recurse | Remove-Item -Recurse 
```

Not only it removes the temporary files that are stored under each of the framework version directories in your local windows installation folder, but it will also delete the ones that keep getting stored under your user AppData folders. This way you’ll be sure to delete files related to both IIS and IISExpress instances.

Just save the above in a *.ps1* file and run it with full privileges every time it seems the debug session is not picking up the right version of the DLL.

Hope this is useful for you. See you on a next post.