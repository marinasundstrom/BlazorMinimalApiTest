# Minimal API with interactive Razor component

Serving Razor component through Minimal API endpoint - in .NET 8.

Based on the ``dotnet new webapi`` template. Adds code that exposes the ``blazor.web.js`` script as a static file.

Endpoint URL: http://localhost:5277/test

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

Since it maps components to routes automatically - And you want the control over the endpoints.

## Adding WebAssembly support

**!! Not tested !!** - I think that you might need to do some more.

Add this to the ``.csproj``:

```xml
<PackageReference Include="Microsoft.AspNetCore.Components.WebAssembly.Server" Version="8.0.0-rc.1.*" />
```

Update the ``Program.cs`` by adding ``AddWebAssemblyComponents``:

```cs
builder.Services.AddRazorComponents()
    .AddServerComponents()
    .AddWebAssemblyComponents();
```

Then set the render mode of, for instance, ``Counter``.

```razor
@attribute [RenderModeWebAssembly]
```

I'm not sure whether this will work with ``RenderModeAuto``.

## Links

* [ASP.NET Core Razor components](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/?view=aspnetcore-7.0)
* [RazorComponentResult Class](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.endpoints.razorcomponentresult?view=aspnetcore-8.0)
