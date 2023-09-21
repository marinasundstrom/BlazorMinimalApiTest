# Minimal API with interactive Razor component

Serving Razor component through Minimal API endpoint - in .NET 8.

Based on the ``dotnet new webapi`` template. Adds code that exposes the ``blazor.web.js`` script as a static file.

Both Server and WebAssembly (below).

Endpoints: http://localhost:5277/server and http://localhost:5277/wasm

Links to resources below.

## Changes

Created components:

* ``Counter`` - Interactive on the server.
* ``MyPage`` - equivalent to an ``index.html`` page (Server-side rendered)

Register Razor component services:

```cs
builder.Services.AddRazorComponents()
    .AddServerComponents();
```

Serve the Blazor JS script:

```cs 
app.UseBlazorWebJS()
```

Map endpoint:

```cs
app.MapGet("/test", () => new RazorComponentResult<BlazorMinimalApi.Components.MyPage>());
```

Map Blazor Hub:

```cs
app.MapBlazorHub();
```

## ``UseBlazorWebJS``

Adds the route serving the ``blazor.web.js``

Based on [this file](https://github.com/dotnet/aspnetcore/blob/e6c7c01bce4fce79bf5bc84098ea8d347ef358cc/src/Components/Endpoints/src/Builder/RazorComponentsEndpointRouteBuilderExtensions.cs#L28).

## Why not invoke ``MapRazorComponents<T>``?

Since it maps components to routes automatically - and you want the control over the endpoints.

## What about layouts?

Page layouts are applied in combination with Blazor ``Router`` and ``RouteView``. We aren't using, and  we can't use this here.

This is thus no direct replacement for the MVC and Razor Views.

## Adding WebAssembly support

First. Create a standalone Blazor WebAssembly project for your client-side code.

```
dotnet new blazorwasm
``````

Reference the client WebAssembly project from the Server project.

### Server project

Add package reference:

``````
dotnet add package Microsoft.AspNetCore.Components.WebAssembly.Server --prerelease
``````

or add this to the ``.csproj``:

```xml
<PackageReference Include="Microsoft.AspNetCore.Components.WebAssembly.Server" Version="8.0.0-rc.1.*" />
```

Update the ``Program.cs`` by adding a call to ``AddWebAssemblyComponents``:

```cs
builder.Services.AddRazorComponents()
    .AddServerComponents()
    .AddWebAssemblyComponents();
```

Use custom extension method that maps endpoint that exposes Blazor framework files.

```cs
app.UseBlazorWebAssemblyRenderMode();
```

Then set the render mode of, for instance, ``Counter``.

```razor
@attribute [RenderModeWebAssembly]
```

It is important that the WebAssembly component lives in the client project, otherwise it will not run in the browser.


## Add routable pages

**This is what is recommended for ordinary apps**

If you want something similar to Razor Pages, then you can enable the standard page component routing. This is provided that you have declared the router in the root ``App`` component.

```cs
app.MapRazorComponents<App>()
    .AddWebAssemblyRenderMode()
    .AddServerRenderMode()
    .AddAdditionalAssemblies(typeof(Counter).Assembly);
```

The router in ``App.razor``:

```cs
<Router AppAssembly="@typeof(App).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">

        </RouteView>
    </Found>
</Router>
```

And add the ``@page`` directive to your page components:

```razor
@page "/my-route"
```

## Notes

I'm not sure whether this will work with ``RenderModeAuto``.

## Links

* [ASP.NET Core Razor components](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/?view=aspnetcore-7.0)
* [RazorComponentResult Class](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.endpoints.razorcomponentresult?view=aspnetcore-8.0)
