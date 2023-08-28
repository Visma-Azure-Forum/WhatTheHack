## Foreword

In this step you will prepare and run local application that includes reference to Azure App Configuration resource that is already in cloud. We will use connection string for communication this time. Try to follow steps as they contain instructions if you simply copy or clone the code here your application will not work (it might lack secrets, NuGet packages, etc.). Referring to contributed code comes in handy when you are stuck or get some errors. We will use and augment this very application in next steps (deployment to cloud, containerization).

## Preparing application

### Prepare Azure App Configuration
  - Deploy App Configuration (standard tier)
  - Add key-values
    | **Key**                          | **Value**                         |
    |----------------------------------|-----------------------------------|
    | TestApp:Settings:BackgroundColor | white                             |
    | TestApp:Settings:FontColor       | black                             |
    | TestApp:Settings:FontSize        | 24                                |
    | TestApp:Settings:Message         | Data from Azure App Configuration |
   - Alternatively, you can use following json to fill settings (choose “:” as separator)
      ```json
      {
        "TestApp": {
          "Settings": {
            "BackgroundColor": "White",
            "FontColor": "Black",
            "FontSize": 24,
            "Message": "Data from Azure App Configuration"
          }
        }
      }
      ```

### Create an ASP.NET Core web app

  - Run command in console this creates application in TestAppConfig folder  
    ```
    dotnet new webapp --output TestAppConfig --framework net6.0
    ```
  - Navigate to TestAppConfig directory
  - Add Microsoft.Azure.AppConfiguration.AspNetCore NuGet package  
    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```
  - Initiate and store secret in [secret manager](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-7.0&tabs=windows#secret-manager)  
    ```
    dotnet user-secrets init
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```
  - You can verify output using
    ```
    dotnet user-secrets list
    ```
  - Open **Program.cs** and add Azure App Configuration as an extra configuration source by calling the AddAzureAppConfiguration method
    ```csharp
    var builder = WebApplication.CreateBuilder(args);

    // Retrieve the connection string
    string connectionString = builder.Configuration.GetConnectionString("AppConfig");
    
    // Load configuration from Azure App Configuration
    builder.Configuration.AddAzureAppConfiguration(connectionString);
    
    // The rest of existing code in program.cs
    // ... ...
    ```

### Read from the App Configuration store

  - Add a Settings.cs file at the root of your project directory. It defines a strongly typed Settings class for the configuration you're going to use. Replace the namespace with the name of your project
    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

  - Bind the TestApp:Settings section in configuration to the Settings object.
    ```csharp
    using TestAppConfig;

    // Existing code in Program.cs
    // ... ...
    
    builder.Services.AddRazorPages();
    
    // Bind configuration "TestApp:Settings" section to the Settings object
    builder.Services.Configure<Settings>(builder.Configuration.GetSection("TestApp:Settings"));
    
    var app = builder.Build();
    
    // The rest of existing code in program.cs
    // ... ...
    ```

  - Open Index.cshtml.cs in the Pages directory, and update the IndexModel class with the following code.
    Add the using Microsoft.Extensions.Options; namespace at the beginning of the file
    ```csharp
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;
    
        public Settings Settings { get; }
    
        public IndexModel(IOptionsSnapshot<Settings> options, ILogger<IndexModel> logger)
        {
            Settings = options.Value;
            _logger = logger;
        }
    }
    ```
  - Open Index.cshtml in the Pages directory, and update the content with the following code
    ```razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    
    <style>
        body {
            background-color: @Model.Settings.BackgroundColor;
        }
    
        h1 {
            color: @Model.Settings.FontColor;
            font-size: @(Model.Settings.FontSize)px;
        }
    </style>
    
    <h1>@Model.Settings.Message</h1>
    ```

### Build and run the app locally
  - dotnet build
  - dotnet run
  - The output of the dotnet run command contains URLs
