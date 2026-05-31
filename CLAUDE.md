# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Build
dotnet build

# Run (HTTP on port 5181, HTTPS on port 7066)
dotnet run --project PegusasServiceHub

# Run with specific profile
dotnet run --project PegusasServiceHub --launch-profile https

# Watch mode (auto-reload on changes)
dotnet watch --project PegusasServiceHub run
```

There are currently no tests in this solution.

## Architecture

**Solution projects:**
- `PegusasServiceHub/` — ASP.NET Core MVC web app (.NET 10), the main application
- `ServiceHub.Domain/` — Class library (.NET 10) intended for domain entities and business logic; currently a placeholder

**PegusasServiceHub follows standard ASP.NET Core MVC conventions:**
- `Controllers/` → request handling
- `Views/` → Razor views; shared layout in `Views/Shared/_Layout.cshtml`
- `Models/` → view models
- `wwwroot/` → static assets (Bootstrap 5, jQuery, site CSS/JS)

**Routing:** Default MVC pattern `{controller=Home}/{action=Index}/{id?}` configured in `Program.cs`.

**Intended direction:** `ServiceHub.Domain` is meant to hold domain logic separate from the web layer — the groundwork for a layered architecture — but no domain entities or interfaces have been added yet.
