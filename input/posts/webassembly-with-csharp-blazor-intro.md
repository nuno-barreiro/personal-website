---
Title: WebAssembly with C# – Introduction to Blazor
Published: 2019-01-09 10:53:00 +0000
Tags: 
  - .NET Core
  - Blazor
  - WebAssembly
---
To better start this year I’ve decided to start learning more about [WebAssembly](https://webassembly.org/) and, because I do mostly work with .NET technologies nowadays, starting out with [Blazor](https://blazor.net/) was an obvious choice.

WebAssembly, for those who are unfamiliar with it, is a open web standard supported out-of-the-box in modern browsers. Is a bytecode format optimized for performance.

Blazor, on the other hand, is a .NET web framework that runs in the browser with WebAssembly. This is possible because a Blazor app compiles all the [C#](https://en.wikipedia.org/wiki/C_Sharp_%28programming_language%29) and [Razor](https://en.wikipedia.org/wiki/ASP.NET_Razor) files into .NET assemblies that are downloaded to the browser, together with the .NET runtime. Blazor then uses JavaScript to bootstrap the .NET runtime that will load all the needed references. JavaScript interoperability also allows for DOM manipulation and direct access to browser API calls.

There’s a lot more that can be said about all the above points but for now, and before starting to dig into code, I think its also necessary to be aware that Blazor apps are built with components, which is nothing more than a piece of UI that can be nested, reused and shared between different projects. Examples of components can be a form, a dialog or even a full page.

We’ll understand even more of all this as we go along. For now lets make sure we have everything we need to start coding.

Environment Setup

In order to be able to create Blazor apps, your system must met the following requirements:
- [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1);
- [Visual Studio 2017](https://visualstudio.microsoft.com/vs/) (15.9 or later) with ASP.NET and Web Development workload enabled;
- [ASP.NET Core Blazor Language Services extension](https://marketplace.visualstudio.com/items?itemName=aspnet.blazor);
- Blazor templates installed which can be done via the command-line using the below command, after installing .NET Core SDK:

``` bash
dotnet new -i Microsoft.AspNetCore.Blazor.Templates 
```

### Create your first Blazor project

To create a new Blazor application you can use the dotnet command line or Visual Studio. I’m a huge fan of using the dotnet CLI to better organize my solutions (maybe more on that on a future post) but for now I’ll keep it simple and I’ll choose the later.

After installing the above requirements, creating a new Blazor project is the same as creating other ASP.NET Core Web Application:
- Select **File > New Project > Web > ASP.NET Core Web Application**;
- Be sure to choose **.NET Core** and **ASP.NET Core 2.1**;
- At this point you’ll have three different Blazor options to choose from. We’ll select the first one **‘Blazor’** for now and later I’ll expand more insight about the other options. Click **OK**.

When finished, you’ll have a project containing some Razor pages, some typical ASP.NET Core code that bootstraps the application and a wwwroot folder containing the index HTML page along with some CSS and other files. Select **Debug > Start Without Debugging** (or CTRL+F5) and the browser will open and show you the default Blazor application.

### Calling it a day

This was just a quick intro on what Blazor and WebAssembly are and what you need to prepare a development environment for creating Blazor applications and how to use Visual Studio to do so.

Next post we’ll look more deeply into Blazor components and layouts. 

See you on the next post.