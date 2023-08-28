## Introduction

This tutorial will take you on a path of writing an application for on-premise purposes that will later on evolve to cloud deployment and as a last step we will create an image that can be easily deployed as a container. Another important part of it will be usage of managed identities that completely replace connection string and passwords together with a demo of Azure App Configuration resource that eases up management application specific settings.

## Preparing application

### Prepare environment
  - Install .NET6 (https://dotnet.microsoft.com/en-us/download/dotnet/6.0)
  - Install VSCode (https://code.visualstudio.com/download)
  - Create VSCode workspace
  - Install Extensions (Azure Account, Azure Tools, Docker, C#)
  - Install Docker for Desktop (https://www.docker.com/products/docker-desktop/)
  - Install PowerShell 7 (https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.3)
  - Install AZ CLI (https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
  - Install Powershell Azure module and (https://learn.microsoft.com/en-us/powershell/azure/install-azure-powershell)
  - Login az login and Connect-AzAccount and select a subscription

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
      ```
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

