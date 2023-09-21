# Minimal API with interactive Razor component

Serving Razor component through Minimal API endpoint - in .NET 8.

Based on the ``dotnet new webapi`` template. Adds code that exposes the ``blazor.web.js`` script as a static file.

Endpoint URL: http://localhost:5277/test

## Changes

Created components:

* ``Counter`` - Interactive on the server.
* ``MyPage`` - equivalent to an ``index.html`` page (Server-side-rendered)

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

## Why not invoking ``MapRazorComponents<T>``?

Since it maps components to routes automatically - And you want the control over the endpoints.

## WebAssembly

**Not tested**

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

Should work with ``RenderModeAuto``.
