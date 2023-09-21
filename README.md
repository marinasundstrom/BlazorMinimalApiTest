# Minimal API with interactive Razor component

Serving Razor component through Minimal API endpoint.

Based on the ``dotnet new webapi`` template.

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