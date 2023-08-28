## Foreword

This tutorial build up on previous steps - 001-PreparingApplication and 002-CloudDeployment - we will make an image from our application and deploy it to cloud using Azure Container Apps which is Microsoft managed Kubernetes Cluster.

### Containerize and deploy to Container App

  - Create Dockerfile in root folder with following contents
    ```Dockerfile
    FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS runtime
    WORKDIR /app
    COPY published/ ./
    ENTRYPOINT ["dotnet", "TestAppConfig.dll"]
    ```
  - Build the image
    ```
    docker build --tag aspnetapp .
    ```
  - Create an Azure Container Registry instance
  - Configure permissions to Managed Identity created earlier (acr pull) and enable admin credentials
  - Login to registry
    ```
    az acr login --name myregistry
    ```
  - Tag the image
    ```
    docker tag aspnetapp myregistry.azurecr.io/aspnetapp:v1
    ```
  - You can check images using following command, you should see at least 2 images
    ```
    docker image ls
    ```
  - Push image to registry using Docker push
    ```
    docker push <login-server>/<image-name>:<tag>
    ```
  - Create Container App using the image
  - Enable ingress traffic on port 80 and traffic from anywhere
  - Create Service Connection to App Configuration

## Additional information

Different service connector types with examples https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet
