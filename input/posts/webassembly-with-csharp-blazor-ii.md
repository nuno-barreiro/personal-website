---
Title: Web Assembly with C# II – Blazor Components
Published: 2019-01-22 22:53:00 +0000
Tags: 
  - .NET Core
  - Blazor
  - WebAssembly
---
> This post is part of a series that introduces WebAssembly with and Blazor, a web framework to build single page applications using C#, Razor and ASP.NET Core. If you would like to see the previous posts, please follow the below links. You can also follow the GitHub repository created for this series [here](https://github.com/lusocoding/wasm-blazor-intro).
> 
> [Part I - WebAssembly with C# – Introduction to Blazor](http://www.nunobarreiro.com/2019/01/webassembly-with-c-introduction-to.html)

In this post let’s begin by looking at the project we created and we’ll look more closely to the Counter component (Pages/Counter.cshtml) to better understand the Blazor components features and how to build, use and share them not only on a single project but across multiple projects.

### Routing

The first line contains a @page directive. This is used to define the routing of the component. After compilation the generated classes are decorated with a [RouteAttribute](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Components.RouteAttribute.html) with the specified template and the runtime looks for those attributes when deciding which components should be rendered.

Multiple route templates can be defined, so it would be possible to add another page directive such as @page “/anothercounter”. From that point on the component would respond for requests either for /counter and /anothercounter. Please check [this commit](https://github.com/lusocoding/wasm-blazor-intro/commit/7236649ff486e98c99fdc17ddce1dee1045d69f8) to see how you could define the new route template and use it on the navigation menu of the application.

Its also possible to pass arguments so we could define a @page “/counter/{currentCount}” and use that argument to set, for example, the initial value of the counter. Optional arguments are not allowed so multiple routes must be defined.

### Razor template

The middle part of the component should be very familiar to anyone that has come across with the Razor syntax in the past. If you’re not familiar with Razor I recommend you to give a read of the syntax reference that you can find on this [link](https://docs.microsoft.com/pt-pt/aspnet/core/mvc/views/razor?view=aspnetcore-2.2) before continuing to read this and the following posts.

### @functions block

The last part of the Counter component is the @functions block, where all the members (methods, properties, etc.) of the component call are defined so that they can be used for component logic and event handling. 

In this component we have a field currentCount that is used to maintain state and a method IncrementCount that is used when handling the onclick event of the button defined in the DOM. This way when the user clicks the button the IncrementCount method is called, the component regenerates its render tree and Blazor applies any modifications to the DOM by comparing the new tree with the previous one, which makes the displayed count to be updated.

### Components parameters and reutilization

Now that we saw the counter component in detail let’s suppose that we wanted to use it inside another component. When compiled a C# class is generated and its name matches the name of the component we created so if we wanted to include the Counter component defined in Counter.cshtml file on the Index component all we have to do is adding the following markup to the Index.cshtml file:

``` html
<Counter />
```

If you start the application you’ll notice that the Counter component is now displayed on the Index component as expected. In this case we can now call the Index component the parent component and Counter is its child component. 

On the parent component markup we could also pass parameters to a child component or provide some content that should be rendered by it by placing it inside the child component tags, for example:

``` html
<Counter Title=”Index Counter”>
This counter is being displayed on the Index component.
</Counter>
```

For this to work some changes need to be made to the Counter component, obviously.
- First we have be declare two private properties (one named Title of type string and another named ChildContent of type [RenderFragment](https://blazor.net/api/Microsoft.AspNetCore.Blazor.RenderFragment.html)) both decorated with the [[Parameter]](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Components.ParameterAttribute.html) attribute; 
- Secondly we then use Razor to display the Title and the ChildContent properties on the component markup;
- Lastly, since this component is also being called directly from the navigation menu, we’ll set a default value for the Title so that it can be displayed when no Title is given. 

All of these changes can be seen on [this commit](https://github.com/lusocoding/wasm-blazor-intro/commit/f27aa81c01a69aa0e6baf47dacbac1001bf9c30d). 

Restart the application and you’ll notice the new Title and Child Content being rendered on the Counter component inside the Index component. If you click on the Counter link of the navigation menu, you’ll also see the default title value being applied.

### Internals
To better understand everything that was said above lets look at the generated DLL after compilation with Visual Studio. The generated code can be seen in the below picture:

![Screenshot of Counter class decompiled](/assets/img/counter_class_decompiled.png)

Please note some of the things we already discussed above and see how exactly the Razor markup that is defined in the .cshtml is taken and converted into the implementation of the overriden BuildRenderTree method.

### Sharing components

As previously stated Blazor components can be shared across multiple projects. For that we need to create a Blazor library and add a reference to it in our current solution. Executing the following commands in our solution folder will do exactly that.

``` bash
dotnet new blazorlib -o WasmWithBlazorComponentsLib
dotnet sln add .\WasmWithBlazorComponentsLib
``` 

Reloading the solution in Visual Studio will show the new project. The Blazor Library template already comes with a default component and we'll now add it to our Index component so it can be rendered in the main page.

First we'll add a reference of the newly created library to the main project (Right click on the main project > Add > Reference). Now we'll need to add the following directive ```Pages/_ViewImports.cshtml```:

``` html
@addTagHelper  WasmWithBlazorComponentsLib.Component1, WasmWithBlazorComponentsLib
```

There are a couple of things that I would like to clarify at this point:
The reason we add the above directive to the ViewImports file is so that the component can be used in all pages that have this file as their base layout, which is something that is happening in all the components of our application. If we are sure we just need to use it in a single component, we could add the directive in the corresponding file instead;
As you can notice the @addTagHelper directive format is ```<namespace>.<component name>, <assembly name>```. However, if the assembly has multiple components and we would like to include all of them, then the wildcard could be used instead, such as ```<namespace>.<component name>, <assembly name>```.
Finally, similar to what we did with the Counter component, we can now add the tag ```<Component1 />``` to the Index component so that it will be rendered on the main page. We can verify this by running the application after all changes are made. All these steps can be seen on [this commit](https://github.com/lusocoding/wasm-blazor-intro/commit/3fe8c61831c79d817e6ff91fdf11f00600abdaff).

### Wrapping up for today

As you can see creating, reusing and sharing Blazor components is very easy and the programming models are very similar to everyone that is familiar with modern .NET web application development.

There is a lot more that can be said about Blazor components and we'll surely discuss that in the next posts.

