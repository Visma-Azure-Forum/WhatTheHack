## Introduction

This tutorial will take you on a path of writing an application for on-premise purposes that will later on evolve to cloud deployment and as a last step we will create an image that can be easily deployed as a container. Another important part of it will be usage of managed identities that completely replace connection string and passwords together with a demo of Azure App Configuration resource that eases up management application specific settings.

This project is cross-platform so you should be able to run in on Linux and MacOS with no major issues but steps were written as seen on Windows OS. Former knowledge with C#, Razor or HTML isn't required but it helps if you already know your way around Azure Portal and basic resources.

**Repository was divided into several folders, each of them builds up on experience/code that you have created before**

## Prepare working environment

It is advised to setup your working environment in following way to minimize technical trouble during this tutorial

  - Install .NET6 (https://dotnet.microsoft.com/en-us/download/dotnet/6.0)
  - Install VSCode (https://code.visualstudio.com/download)
  - Create VSCode workspace
  - Install Extensions (Azure Account, Azure Tools, Docker, C#)
  - Install Docker for Desktop (https://www.docker.com/products/docker-desktop/)
  - Install PowerShell 7 (https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.3)
  - Install AZ CLI (https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
  - Install Powershell Azure module and (https://learn.microsoft.com/en-us/powershell/azure/install-azure-powershell)
  - Login az login and Connect-AzAccount and select a subscription
