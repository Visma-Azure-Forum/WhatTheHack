## Foreword

This step takes our application - built in step 001-PreparingApplication - to the cloud. Along the way we will update it to use managed identities rather than connection string.

### Preparing for cloud deployment

  - Add the Azure.Identity package:
    ```    
    dotnet add package Azure.Identity
    ```
  - Add following lines to your code **(specify which file)**:
    ```
    using Azure.Identity;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```
  - Remove locally stored connection string reference
    ```csharp
    // Retrieve the connection string
    string connectionString = builder.Configuration.GetConnectionString("AppConfig");
    ```
  - Update *builder.Configuration.AddAzureAppConfiguration block* to use managed identity for authentication
    ```csharp
    var builder = WebApplication.CreateBuilder(args);

    // Load configuration from Azure App Configuration
    builder.Configuration.AddAzureAppConfiguration(options =>
    {
        // Service Connector configured AZURE_APPCONFIGURATION_ENDPOINT and AZURE_APPCONFIGURATION_CLIENTID at Azure WebApp's AppSetting already.
        string appConfigurationEndpoint = Environment.GetEnvironmentVariable("AZURE_APPCONFIGURATION_ENDPOINT");
        string userAssignedClientId = Environment.GetEnvironmentVariable("AZURE_APPCONFIGURATION_CLIENTID");
        var credential = new DefaultAzureCredential(new DefaultAzureCredentialOptions { ManagedIdentityClientId = userAssignedClientId });
    
        if (!string.IsNullOrEmpty(appConfigurationEndpoint))
        {
            options.Connect(new Uri(appConfigurationEndpoint), credential)
                   // Load all keys that start with `WebDemo:` and have no label
                   .Select("TestApp:*", LabelFilter.Null)
                   // Configure to reload configuration if the registered key 'SampleApplication:Settings:Messages' is modified.
                   // Use the default cache expiration of 30 seconds. It can be overriden via AzureAppConfigurationRefreshOptions.SetCacheExpiration.
                   .ConfigureRefresh(refreshOptions =>
                   {
                       refreshOptions.Register("TestApp:Settings:Message", refreshAll: true);
                   });
        }
    });
    
    // Add services to the container.
    builder.Services.AddRazorPages();
    ```

  - Add Azure Container App middleware
    ```csharp
    // Add services to the container.
    builder.Services.AddRazorPages();
    
    // Add Azure App Configuration middleware to the container of services.
    builder.Services.AddAzureAppConfiguration();
    
    // Bind configuration "TestApp:Settings" section to the Settings object
    builder.Services.Configure<Settings>(builder.Configuration.GetSection("TestApp:Settings"));
    ```
  - Use Azure App Configuration middleware for dynamic configuration refresh
    ```csharp
    // Configure the HTTP request pipeline.
    if (!app.Environment.IsDevelopment())
    {
        app.UseExceptionHandler("/Error");
        // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
        app.UseHsts();
    }
    
    	// Use Azure App Configuration middleware for dynamic configuration refresh.
    app.UseAzureAppConfiguration();
    
    	app.UseHttpsRedirection();
    app.UseStaticFiles();
    ```
  - Build the application with output to custom folder
    ```
    dotnet publish -c Release -o published
    ```

### Deploy to cloud (App Service)
  - Deploy app service with free plan
  - Create Managed Identity
  - Create Service Connector inside of App Service
    - Point it to App Configuration
    - Use User assigned managed identity we created earlier
    - In Advanced options select Data Reader
  - Deploy application using VSCode extension **(specify extension name)**
