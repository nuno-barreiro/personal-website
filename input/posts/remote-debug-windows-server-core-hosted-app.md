---
Title: Remote Debug of a Windows Server Core hosted application from VS 2017
Published: 2018-12-04 14:51:00 +0000
Tags: 
  - Visual Studio
  - Debugging
---
If you have an application hosted in a Windows Server 2016 Core machine and would like to debug it directly from Visual Studio 2017, then its very simple to configure remote debugging.

First, you must download the **Visual Studio Remote Tools** from this [link](https://visualstudio.microsoft.com/downloads/?q=remote+tools#remote-tools-for-visual-studio-2017). Save it somewhere on the machine disk, for example *"C:\temp\vs_remotetools.exe"*.

Next, using the command-line as an administrator, invoke the following command:

``` powershell
C:\temp\vs_remotetools.exe /install /quiet
```

This will silently install the remote tools. Usually, I like to have the tools running as a service so I don't have to start those each time. This is very convenient specially for development environments.

Go to *"C:\Program Files\Microsoft Visual Studio 15.0\Common7\IDE"* and execute **rdbgwiz.exe**. Despite being a no-GUI edition, there are some applications that do show some user interface and fortunately this is one of those.

Click next and on the second screen be sure to select **"Run the Visual Studio 2017 Remote Debugger service"**. You can then enter the credentials to use when running the service or you can leave the default *"LocalSystem"* account, if you prefer. After clicking next you'll have the change to select the type of networks to use for debugging and if all goes well you'll have a message on the last screen saying the service was configured correctly.

To ensure the service is running execute the following using PowerShell:

``` powershell
Get-Service -Name msvsmon150
```

Now that the remote is up and running, open your project with Visual Studio 2017, set the breakpoints you need and open the **"Attach to Process"** dialog (Debug > Attach to Process). Input the IP of the machine where you just installed the remote debugger and use port **4022** which is the default. After that click on Refresh and you should see the list of processes running on that machine, like the below figure:

![Visual Studio - Attach to Process dialog](/assets/img/vs_attach_to_process.png)

Final step, as you may know, just select the process you want and click on Attach. Execute any necessary steps and the debugger should now stop in the breakpoints you've setup.

If you'd like to run the debugger tools "on-demand" instead of running as a service, then you can run **msvsmon.exe** each time that's needed, which by default will be located under *"C:\Program Files\Microsoft Visual Studio 15.0\Common7\IDE\Remote Debugger\x64"*.

Hope this is useful to you. Feel free to comment. See you on a next post.
