# Configuration & Externalized Settings Inventory

Configuration is primarily held in the classic ASP.NET `Web.config`, with separate Debug and Release transform files. No external configuration service or secret store integration was identified; the configured database connection uses integrated security.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| ASP.NET application settings and connection strings | Runtime configuration | `Web.config` | Includes LocalDB connection, MVC settings, request limits, and notification queue path |
| Debug transform | Environment transform | `Web.Debug.config` | Applies Debug-specific Web.config changes |
| Release transform | Environment transform | `Web.Release.config` | Applies Release-specific Web.config changes |
| Views configuration | MVC/Razor configuration | `Views/Web.config` | View-specific configuration |
| NuGet package manifest | Dependency configuration | `packages.config` | Pinned package versions |
| MSBuild project | Build configuration | `ContosoUniversity.csproj` | Framework, configurations, references, and IIS Express settings |
| Solution | Build composition | `ContosoUniversity.sln` | Contains the ContosoUniversity web project |

No Docker, Kubernetes, external config repository, Azure App Configuration, Key Vault, Vault, or AWS Secrets Manager reference was identified.

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | MSBuild `Debug` configuration | Debug symbols enabled, optimization disabled, `DEBUG;TRACE` symbols | No profile-specific package changes identified |
| Release | MSBuild `Release` configuration | Optimized build with PDB-only debug information and `TRACE` symbol | No profile-specific package changes identified |

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Default | IIS/IIS Express application startup | `Web.config` | LocalDB integrated-security connection, Windows authentication enabled for IIS Express, anonymous authentication disabled |
| Debug | Web.config transform during Debug deployment | `Web.Debug.config` | No specific override was confirmed from the transform |
| Release | Web.config transform during Release deployment | `Web.Release.config` | No specific override was confirmed from the transform |

No `ASPNETCORE_ENVIRONMENT` or multiple runtime profile mechanism is configured.

## Properties Inventory

### ContosoUniversity

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `DefaultConnection` | LocalDB connection string with integrated security; database `ContosoUniversityNoAuthEFCore` | Default | `Web.config` connection strings |
| `webpages:Version` | `3.0.0.0` | Default | `Web.config` appSettings |
| `webpages:Enabled` | `false` | Default | `Web.config` appSettings |
| `ClientValidationEnabled` | `true` | Default | `Web.config` appSettings |
| `UnobtrusiveJavaScriptEnabled` | `true` | Default | `Web.config` appSettings |
| `NotificationQueuePath` | `.\Private$\ContosoUniversityNotifications` | Default | `Web.config` appSettings; code fallback is the same |
| `compilation.debug` | `true` | Default | `Web.config` |
| `compilation.targetFramework` | `4.8` | Default | `Web.config` |
| `httpRuntime.targetFramework` | `4.8` | Default | `Web.config` |
| `httpRuntime.maxRequestLength` | `10240` KB | Default | `Web.config` |
| `httpRuntime.executionTimeout` | `3600` seconds | Default | `Web.config` |
| `requestLimits.maxAllowedContentLength` | `10485760` bytes | Default | `Web.config` |
| `TargetFrameworkVersion` | `v4.8` | Build | `ContosoUniversity.csproj` |
| `UseIISExpress` | `true` | Build | `ContosoUniversity.csproj` |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| ContosoUniversity | IIS/IIS Express; .NET Framework 4.8 | Not specified | Not specified |

No explicit JVM parameters, container resource limits, CPU limits, or instance scaling configuration were found.

## Startup Dependency Chain

1. IIS/IIS Express loads the application and `Global.asax`.
2. `Application_Start` registers MVC areas, filters, routes, and bundles.
3. Startup creates a `SchoolContext` using the SQL Server connection and invokes `DbInitializer`.
4. Controllers create their own database context and notification service as requests arrive.

No readiness probe, dependency wait script, startup retry, or health-check mechanism was identified.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| `DefaultConnection` | Database connection | Integrated security; no password is stored in the checked-in connection string |
| `NotificationQueuePath` | Local resource location | Non-secret path |

No hard-coded database password, API key, cryptographic key, encrypted configuration value, or secret-store reference was identified.

### Secrets Provisioning Workflow

No application secret provisioning workflow is configured. The database connection relies on the Windows identity used by IIS/IIS Express; no application-specific managed identity, service principal, or external secret binding is documented.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| None identified | — | No feature-flag framework or feature toggles were found |

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| .NET Framework | 4.8 | `ContosoUniversity.csproj`, `Web.config` |
| ASP.NET MVC | 5.2.9 | `packages.config` |
| Entity Framework Core | 3.1.32 | `packages.config` |
| Microsoft.Data.SqlClient | 2.1.4 | `packages.config` |
| Newtonsoft.Json | 13.0.3 | `packages.config` |
| Bootstrap | 5.3.3 | `packages.config` |
| jQuery | 3.7.1 | `packages.config` |
| MSBuild project format | ToolsVersion 15.0 | `ContosoUniversity.csproj` |
