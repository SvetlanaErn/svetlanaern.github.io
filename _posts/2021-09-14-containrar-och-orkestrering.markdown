---
layout: post
title:  "Containrar och orkestrering"
date:   2021-09-14 20:04:59 +0200
author: SvetlanaErn
---
För att bygga en container med ASP .Net webbapplikation behöver man installera [Docker Desktop](https://docs.docker.com/get-docker/) och följ de tre stegen:


1. Lägga till Dockerfile (beskriver hur container byggs) till [ASP .Net projekten](https://github.com/SvetlanaErn/SimpleWebHalloWorld).
2. Bygga Image med hjälp av följande CLI kommando:
```
docker build -t image_name .
```
3. Skapa container (instance av Image) och kör det
```
docker run -d -p 8080:80 --name container_name image_name
```

Jag använde mig autogenererad dockerfile (högerklick på projekten i solution explorer i VS -> add -> Docker support…):
```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:3.1 AS base  # definerar basimage
WORKDIR /app
EXPOSE 80
EXPOSE 443

# build projekt
FROM mcr.microsoft.com/dotnet/sdk:3.1 AS build
WORKDIR /src
COPY ["SimpleWebHalloWorld.csproj", "."]
RUN dotnet restore "./SimpleWebHalloWorld.csproj"
COPY . . 
WORKDIR "/src/."
RUN dotnet build "SimpleWebHalloWorld.csproj" -c Release -o /app/build

# skapas artefakt
FROM build AS publish
RUN dotnet publish "SimpleWebHalloWorld.csproj" -c Release -o /app/publish 

# kopierar artefakt till slutimage 
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "SimpleWebHalloWorld.dll"]
```


## CI och CD pipeline på GitHub.
För att skapa Docker image och publicera det till ”GitHub packages” med hjälp av GitHub Actions använde jag följande pipeline:
```yaml
name: CI_CD
on: [push]
jobs:
# Det första jobbet är att bygga applikation
  build: 
    name: Build
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 5.0.x
    - name: build
      run: dotnet build ./SimpleWebHalloWorld.sln
  
# Det andra jobbet är att bygga docker image och pusha det till GitHub package storage
  Build-and-Push-Docker-Image:
     name: Docker Build, Push
     runs-on: ubuntu-latest
     env: 
      working-directory: .
      
     steps: 
      - name: Checkout code
        uses: actions/checkout@v2.3.4
      - name: Login to GitHub Container Registry
      # GitHub Action för att logga in i GitHub Container Registry
        uses: docker/login-action@v1.10.0 
        with:
         registry: ghcr.io
         username: ${{github.actor}}; # får användares namn
         password: ${{secrets.GITHUB_TOKEN}} # GitHub token för att autentisera sig i GitHub Container Registry
      - name: Build and push
        id: docker_build
        # GitHub Action som bygger image och pushar det till GitHub Container Registry
        uses: docker/build-push-action@v2.7.0  
        with:
         push: true
         context: .
         tags: |
          ghcr.io/svetlanaern/simplewebhalloworld:latest
          ghcr.io/svetlanaern/simplewebhalloworld:${{github.run_number}}    
```
För att autentisera sig i GitHub Container Registry [rekommenderas](https://github.com/docker/login-action#github-container-registry) att använda  GITHUB_TOKEN som GitHub skapar automatiskt.


*Källor:*


* [About the GITHUB_TOKEN secret](https://docs.github.com/en/actions/reference/authentication-in-a-workflow)
* [How YOU can Dockerize a .Net Core app](https://softchris.github.io/pages/dotnet-dockerize.html)



