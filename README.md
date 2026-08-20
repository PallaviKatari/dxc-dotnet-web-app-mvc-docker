# DXC .NET Web App MVC Docker

A basic ASP.NET Core MVC application targeting .NET 8. The application can run locally with the .NET CLI or in a Docker container.

## Prerequisites

Install the following tools:

- [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- Git

Verify the installations:

```powershell
dotnet --version
docker --version
git --version
```

## Project Structure

```text
DxcDotnetWebAppMvcDocker/
|-- Controllers/       MVC controllers
|-- Models/            View models
|-- Views/             Razor views
|-- wwwroot/           Static files and client libraries
|-- Dockerfile         Multi-stage Docker build
|-- Program.cs         Application startup
|-- *.csproj           .NET project definition
`-- .dockerignore      Docker build exclusions
```

## Run Locally

From the repository root:

```powershell
Set-Location .\DxcDotnetWebAppMvcDocker
dotnet restore
dotnet build
dotnet run --no-https
```

Open the URL printed by the application, usually `http://localhost:5000` or `http://localhost:5001`.

To stop the local application, press `Ctrl+C`.

## Build the Docker Image

From the repository root:

```powershell
docker build -t dxc-dotnet-web-app-mvc:latest .\DxcDotnetWebAppMvcDocker
```

The Dockerfile uses two stages:

1. The .NET 8 SDK image restores, builds, and publishes the application.
2. The smaller ASP.NET 8 runtime image runs the published application.

The application listens on port `8080` inside the container.

## Run with Docker

Start the container and map host port `8088` to container port `8080`:

```powershell
docker run -d --name dxc-dotnet-web-app-mvc-container -p 8088:8080 dxc-dotnet-web-app-mvc:latest
```

Open the application at <http://localhost:8088/>.

Verify the container is running:

```powershell
docker ps --filter name=dxc-dotnet-web-app-mvc-container
curl.exe -I http://localhost:8088/
```

View application logs:

```powershell
docker logs dxc-dotnet-web-app-mvc-container
```

Stop and remove the container:

```powershell
docker rm -f dxc-dotnet-web-app-mvc-container
```

If port `8088` is unavailable, choose another host port while keeping `8080` as the container port. For example:

```powershell
docker run -d --name dxc-dotnet-web-app-mvc-container -p 8090:8080 dxc-dotnet-web-app-mvc:latest
```

Then open <http://localhost:8090/>.

## Test the Application

Build the application locally:

```powershell
dotnet build .\DxcDotnetWebAppMvcDocker\DxcDotnetWebAppMvcDocker.csproj
```

After starting the container, check the home page returns HTTP 200:

```powershell
curl.exe -I http://localhost:8088/
```

## Git Workflow

Check the working tree:

```powershell
git status
git branch --show-current
```

Create a commit:

```powershell
git add README.md .gitignore DxcDotnetWebAppMvcDocker
git commit -m "Create .NET 8 MVC Docker app"
```

Push the `main` branch to the configured remote:

```powershell
git push -u origin main
```

## Configuration

The container sets `ASPNETCORE_URLS=http://+:8080`, so it serves HTTP on port `8080` inside Docker. HTTPS is disabled for the container-focused local setup.
