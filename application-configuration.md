# Application Configuration

## [Default application configuration sources](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-6.0#default-application-configuration-sources)

[WebApplication.CreateBuilder](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.webapplication.createbuilder) initializes a new instance of the [WebApplicationBuilder](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.webapplicationbuilder) class with preconfigured defaults. The initialized WebApplicationBuilder (builder) provides default configuration for the app in the following order, from highest to lowest priority:

1. Command-line arguments using the [Command-line configuration provider.](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration-providers#command-line-configuration-provider)
2. Environment variables using the [Environment Variables configuration provider.](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider)
3. [App secrets](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets) when the app runs in the Development environment.
4. appsettings.Environment.json using the [JSON configuration provider.](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration-providers#file-configuration-provider) For example, `appsettings.Production.json` and `appsettings.Development.json`.
5. `appsettings.json` using the [JSON configuration provider.](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration-providers#file-configuration-provider)
6. A fallback to the host configuration described in the next section.

---

## Setup `webapi` Project

1. Create `webapi` Project

   ```sh
   dotnet new webapi -o api
   ```

2. Target Files

   ```sh
   api
   |- Properties
       |- launchSettings.json
   |- appsettings.json
   |- appsettings.Development.json
   |- Program.cs
   ```

3. Create `appsettings` with Specific Environment(UAT)

   - Create File Call `appsettings.UAT.json`
   - Copy Detail from `appsettings.Development.json` to `appsettings.UAT.json`

4. Add Data `"Env": "Hello Prod"` to `*.json`

   - `appsettings.json`

     ```json
     {
       "Logging": {
         "LogLevel": {
           "Default": "Information",
           "Microsoft.AspNetCore": "Warning"
         }
       },
       "AllowedHosts": "*",
       "Env": "Hello Prod"
     }
     ```

   - `appsettings.Development.json`

     ```json
     {
       "Logging": {
         "LogLevel": {
           "Default": "Information",
           "Microsoft.AspNetCore": "Warning"
         }
       },
       "Env": "Hello Dev"
     }
     ```

   - `appsettings.UAT.json`

     ```json
     {
       "Logging": {
         "LogLevel": {
           "Default": "Information",
           "Microsoft.AspNetCore": "Warning"
         }
       },
       "Env": "Hello UAT"
     }
     ```

5. Add `api-uat` Profile to `launchSettings.json`

   ```json
   "api": {
     "commandName": "Project",
     "dotnetRunMessages": true,
     "launchBrowser": true,
     "launchUrl": "swagger",
     "applicationUrl": "https://localhost:5229;http://localhost:5228",
     "environmentVariables": {
       "ASPNETCORE_ENVIRONMENT": "Development"
     }
   },
   "api-uat": {
     "commandName": "Project",
     "dotnetRunMessages": true,
     "launchBrowser": true,
     "launchUrl": "swagger",
     "applicationUrl": "https://localhost:5229;http://localhost:5228",
     "environmentVariables": {
       "ASPNETCORE_ENVIRONMENT": "UAT"
     }
   },
   "IIS Express": {
       ...
   }
   ```

6. Add Following lines to `Program.cs`

   - between `builder.Services.AddSwaggerGen();` and `var app = builder.Build();`

     ```cs
     builder.Services.AddSwaggerGen();


     // Additional Code for builder
     string builderEnv = builder.Configuration.GetValue<string>("Env");
     Console.WriteLine("Builder Env: " + builderEnv);

     string builderUrl = builder.Configuration.GetValue<string>("ASPNETCORE_URLS");
     Console.WriteLine("Builder URL: " + builderUrl);
     // End of Additional Code for builder


     var app = builder.Build();
     ```

   - between `var app = builder.Build();` and `if (app.Environment.IsDevelopment())`

     ```cs
     var app = builder.Build();


     // Additional Code for app
     string appEnv = app.Configuration.GetValue<string>("Env");
     Console.WriteLine("App Env: " + appEnv);

     string appUrl = app.Configuration.GetValue<string>("ASPNETCORE_URLS");
     Console.WriteLine("App URL: " + appUrl);
     // End of Additional Code for app


     // Configure the HTTP request pipeline.
     if (app.Environment.IsDevelopment())
     ```

7. Changed Files

   ```sh
   api
   |- Properties
       |- launchSettings.json
   |- appsettings.Development.json
   |- appsettings.json
   |- appsettings.UAT.json
   |- Program.cs
   ```

---

## Development Mode

### Run Development and Launching

1. Run with Default Environment

   ```sh
   dotnet run
   ```

   - output

     ```sh
     Builder Env: Hello Dev
     Builder URL: https://localhost:5229;http://localhost:5228
     App Env: Hello Dev
     App URL: https://localhost:5229;http://localhost:5228
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: https://localhost:5229
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://localhost:5228
     info: Microsoft.Hosting.Lifetime[0]
         Application started. Press Ctrl+C to shut down.
     info: Microsoft.Hosting.Lifetime[0]
         Hosting environment: Development
     ```

2. Run with Specific Environment

   ```sh
   dotnet run --launch-profile [profile name]
   ```

   eg.

   ```sh
   dotnet run --launch-profile api
   ```

   - output

     ```sh
     Builder Env: Hello Dev
     Builder URL: https://localhost:5229;http://localhost:5228
     App Env: Hello Dev
     App URL: https://localhost:5229;http://localhost:5228
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: https://localhost:5229
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://localhost:5228
     info: Microsoft.Hosting.Lifetime[0]
         Application started. Press Ctrl+C to shut down.
     info: Microsoft.Hosting.Lifetime[0]
         Hosting environment: Development
     ```

   ```sh
   dotnet run --launch-profile api-uat
   ```

   - output

     ```sh
     Builder Env: Hello UAT
     Builder URL: https://localhost:5229;http://localhost:5228
     App Env: Hello UAT
     App URL: https://localhost:5229;http://localhost:5228
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: https://localhost:5229
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://localhost:5228
     info: Microsoft.Hosting.Lifetime[0]
         Application started. Press Ctrl+C to shut down.
     info: Microsoft.Hosting.Lifetime[0]
         Hosting environment: UAT
     ```

### Run Development without Launching

```sh
dotnet run --no-launch-profile
```

- output

  ```sh
  Builder Env: Hello Prod
  Builder URL:
  App Env: Hello Prod
  App URL:
  info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5000
  info: Microsoft.Hosting.Lifetime[14]
      Now listening on: https://localhost:5001
  info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
  info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
  ```

### Run with Environment Variables

Default Environment: `Production`, `Staging`, and `Development`

1. Run with `Development` Environment

   - linux, mac

     ```sh
     ASPNETCORE_ENVIRONMENT=Development dotnet run --no-launch-profile
     ```

     - output

       ```sh
       Builder Env: Hello Dev
       Builder URL:
       App Env: Hello Dev
       App URL:
       info: Microsoft.Hosting.Lifetime[14]
           Now listening on: https://localhost:5001
       info: Microsoft.Hosting.Lifetime[14]
           Now listening on: http://localhost:5000
       info: Microsoft.Hosting.Lifetime[0]
           Application started. Press Ctrl+C to shut down.
       info: Microsoft.Hosting.Lifetime[0]
           Hosting environment: Development
       ```

   - windows

     ```sh
     set ASPNETCORE_ENVIRONMENT=Development
     dotnet run --no-launch-profile
     ```

     - output

       ```sh
       Builder Env: Hello Dev
       Builder URL:
       App Env: Hello Dev
       App URL:
       info: Microsoft.Hosting.Lifetime[14]
           Now listening on: https://localhost:5001
       info: Microsoft.Hosting.Lifetime[14]
           Now listening on: http://localhost:5000
       info: Microsoft.Hosting.Lifetime[0]
           Application started. Press Ctrl+C to shut down.
       info: Microsoft.Hosting.Lifetime[0]
           Hosting environment: Development
       ```

2. Run with `UAT` Environment

   - linux, mac

     ```sh
     ASPNETCORE_ENVIRONMENT=UAT dotnet run --no-launch-profile
     ```

     - output

       ```sh
       Builder Env: Hello UAT
       Builder URL:
       App Env: Hello UAT
       App URL:
       info: Microsoft.Hosting.Lifetime[14]
           Now listening on: https://localhost:5000
       info: Microsoft.Hosting.Lifetime[14]
           Now listening on: http://localhost:5001
       info: Microsoft.Hosting.Lifetime[0]
           Application started. Press Ctrl+C to shut down.
       info: Microsoft.Hosting.Lifetime[0]
           Hosting environment: Development
       ```

   - windows

     ```sh
     set ASPNETCORE_ENVIRONMENT=UAT
     dotnet run --no-launch-profile
     ```

     - output

       ```sh
       Builder Env: Hello UAT
       Builder URL:
       App Env: Hello UAT
       App URL:
       info: Microsoft.Hosting.Lifetime[14]
           Now listening on: https://localhost:5000
       info: Microsoft.Hosting.Lifetime[14]
           Now listening on: http://localhost:5001
       info: Microsoft.Hosting.Lifetime[0]
           Application started. Press Ctrl+C to shut down.
       info: Microsoft.Hosting.Lifetime[0]
           Hosting environment: Development
       ```

### Run with Command-line Arguments

```sh
dotnet run --environment UAT
```

- output

  ```sh
  Builder Env: Hello UAT
  Builder URL: https://localhost:5229;http://localhost:5228
  App Env: Hello UAT
  App URL: https://localhost:5229;http://localhost:5228
  info: Microsoft.Hosting.Lifetime[14]
      Now listening on: https://localhost:5229
  info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5228
  info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
  info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: UAT
  ```

```sh
dotnet run --environment Development
```

- output

  ```sh
  Builder Env: Hello Dev
  Builder URL: https://localhost:5229;http://localhost:5228
  App Env: Hello Dev
  App URL: https://localhost:5229;http://localhost:5228
  info: Microsoft.Hosting.Lifetime[14]
      Now listening on: https://localhost:5229
  info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5228
  info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
  info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
  ```

```sh
dotnet run --environment Production
```

- output

  ```sh
  Builder Env: Hello Prod
  Builder URL: https://localhost:5229;http://localhost:5228
  App Env: Hello Prod
  App URL: https://localhost:5229;http://localhost:5228
  info: Microsoft.Hosting.Lifetime[14]
      Now listening on: https://localhost:5229
  info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5228
  info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
  info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
  ```

---

## Production Mode

### Build and Publish

1. Build and Default Publish

   ```sh
   dotnet build
   dotnet publish
   ```

2. Build and Publish to Specific Directory

   ```sh
   dotnet build
   dotnet publish -o <output directory>
   ```

### Run Artifact

1. Run with `Environment Variables`

   ```sh
   ASPNETCORE_ENVIRONMENT=Development bin/Debug/net6.0/publish/api
   ```

   - output

     ```sh
     Builder Env: Hello Dev
     Builder URL:
     App Env: Hello Dev
     App URL:
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://localhost:5000
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: https://localhost:5001
     info: Microsoft.Hosting.Lifetime[0]
         Application started. Press Ctrl+C to shut down.
     info: Microsoft.Hosting.Lifetime[0]
         Hosting environment: Development
     ```

   ```sh
   ASPNETCORE_ENVIRONMENT=UAT bin/Debug/net6.0/publish/api
   ```

   - output

     ```sh
     Builder Env: Hello UAT
     Builder URL:
     App Env: Hello UAT
     App URL:
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://localhost:5000
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: https://localhost:5001
     info: Microsoft.Hosting.Lifetime[0]
         Application started. Press Ctrl+C to shut down.
     info: Microsoft.Hosting.Lifetime[0]
         Hosting environment: UAT
     ```

   ```sh
   ASPNETCORE_URLS='http://+:5432;https://+:5433' ASPNETCORE_ENVIRONMENT=UAT bin/Debug/net6.0/publish/api
   ```

   - output

     ```sh
     Builder Env: Hello UAT
     Builder URL: http://+:5432;https://+:5433
     App Env: Hello UAT
     App URL: http://+:5432;https://+:5433
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://[::]:5432
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: https://[::]:5433
     info: Microsoft.Hosting.Lifetime[0]
         Application started. Press Ctrl+C to shut down.
     info: Microsoft.Hosting.Lifetime[0]
         Hosting environment: UAT
     ```

2. Run with `Command-line Arguments`

   ```sh
   bin/Debug/net6.0/publish/api --environment Development
   ```

   - output

     ```sh
     Builder Env: Hello Dev
     Builder URL:
     App Env: Hello Dev
     App URL:
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://localhost:5000
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: https://localhost:5001
     info: Microsoft.Hosting.Lifetime[0]
         Application started. Press Ctrl+C to shut down.
     info: Microsoft.Hosting.Lifetime[0]
         Hosting environment: Development
     ```

   ```sh
   bin/Debug/net6.0/publish/api --environment Development --urls http://+:5432;https://+:5433
   ```

   - output

     ```sh
     Builder Env: Hello Dev
     Builder URL:
     App Env: Hello Dev
     App URL:
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://[::]:5432
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: https://[::]:5433
     info: Microsoft.Hosting.Lifetime[0]
         Application started. Press Ctrl+C to shut down.
     info: Microsoft.Hosting.Lifetime[0]
         Hosting environment: Development
     ```

3. Replace `Environment Variable`s with `Command-line Arguments`

   ```sh
   ASPNETCORE_ENVIRONMENT=Development bin/Debug/net6.0/publish/api --environment UAT
   ```

   - output

     ```sh
     Builder Env: Hello UAT
     Builder URL:
     App Env: Hello UAT
     App URL:
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://localhost:5000
     info: Microsoft.Hosting.Lifetime[14]
         Now listening on: https://localhost:5001
     info: Microsoft.Hosting.Lifetime[0]
         Application started. Press Ctrl+C to shut down.
     info: Microsoft.Hosting.Lifetime[0]
         Hosting environment: UAT
     ```

4. Add Data `"Credential__Default": ""` to `*.json`

   - `appsettings.json`

     ```json
     {
       "Logging": {
         "LogLevel": {
           "Default": "Information",
           "Microsoft.AspNetCore": "Warning"
         }
       },
       "AllowedHosts": "*",
       "Env": "Hello Prod",
       "Credential": {
        "Default": ""
       }
     }
     ```

   - `appsettings.Development.json`

     ```json
     {
       "Logging": {
         "LogLevel": {
           "Default": "Information",
           "Microsoft.AspNetCore": "Warning"
         }
       },
       "Env": "Hello Dev",
       "Credential": {
        "Default": "Dev Credential"
       }
     }
     ```

   - `appsettings.UAT.json`

     ```json
     {
       "Logging": {
         "LogLevel": {
           "Default": "Information",
           "Microsoft.AspNetCore": "Warning"
         }
       },
       "Env": "Hello UAT",
       "Credential": {
        "Default": "UAT Credential"
       }
     }
     ```

5. Add Following lines to `Program.cs`

   - after `Console.WriteLine("Builder URL: " + builderUrl);`

     ```cs
     string builderUrl = builder.Configuration.GetValue<string>("ASPNETCORE_URLS");
     Console.WriteLine("Builder URL: " + builderUrl);

     string credential = builder.Configuration.GetValue<string>("Credential:Default");
     Console.WriteLine("Credential: " + credential);
     // End of Additional Code for builder


     var app = builder.Build();
     ```

6. Run app and set `Credential` value with `Credential__Default`

   ```sh
   Credential__Default="Custom Credential" bin/Debug/net6.0/publish/api
   ```

---

## References

1. [https://learn.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-6.0](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-6.0)
2. [https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-6.0](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-6.0)
3. [https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration)
