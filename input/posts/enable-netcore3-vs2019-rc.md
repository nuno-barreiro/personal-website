---
Title: Enable .NET Core 3.0 in Visual Studio 2019 RC
Published: 2019-03-12 14:24:00 +0000
Tags: 
  - .NET Core
  - Visual Studio
---

If you're anything like me, as soon as I saw the [announcement of the Visual Studio 2019 Release Candidate](https://devblogs.microsoft.com/visualstudio/visual-studio-2019-release-candidate-rc-now-available/) I installed and started using it to be able to experiment all the new features the VS Team is giving us. And let me tell ya... this new version is fast!

However I come across with a peculiar issue when trying to work in a .NET Core 3 application. 

![NETSDK1 error](/assets/img/vs2019_netcore3prev_error.png)

At the time of this writing, .NET Core 3 is still in preview mode and as far as I knew it was only supported in VS 2019 preview. But it didn't make no sense at all if the preview version supports it and RC doesn't. Fortunately the reason for this is only that .NET Core preview versions are not enabled by default in VS 2019 RC.

To enable those and be able to work with .NET Core 3, go to **Tools > Options >> Projects and Solutions > .NET Core** and make sure to check the option "Use previews of the .NET Core SDK". Do restart VS 2019 RC afterwards since it's very likely this will only work after you do that.

![.NET Core options dialog](/assets/img/vs2019_netcore3prev_enable.png)

Hope this info was of some help to you. See you on a next post!