# Docker for .NetCore API

# Overview
Docker is a containerization platform
* can **package** (build) your application into images
* run the images as **containers** on any platform that can run **docker**
* docker image contains everything it needs to run
* docker contains (run time) are isolated by design
* Can run micro-services as containers, 
* simplifies configuration.
* Can run multiple versions of the same image


A image is a template ( like an application class), a run time is like an object.

# Tools
* Visual Studio Code
* Visual Studio Plugin **docker**
Created by Microsoft, has more than 6 Million downloads. The Docker extension makes it easy to build, manage, and deploy containerized applications from Visual Studio Code. It also provides one-click debugging of Node.js, Python, and .NET Core inside a container.

* .Net Core 3.1 SDK
* Postman / Curl / Web Browser
* Docker Desktop / CE
* Docker 


# Installing Docker

# Creating Images Steps

## Step 1 - Create App
* Tutorial used Visual Studio to build a basic .net command line webApi application, this uses the template to create the default weatherAPI sample application

## Step 2 - Configure HTTPS and Self Signed Certificates
### Create Default WeatherAPI application
* When the application is run, it will using settings in the launchsettings.json file. Under the profiles group there is a profile for IISExpress and another for the native running of the application under Kestrel, this is what docker will use. This specified the application URL's.

### Start up configuration
* In the startup class, in **Configure method** has the statement **app.UseHttpRedirection**, this redirects flow to https, this required a certificate if using HTTPS. You can for develop without https by removing https from the launchsettings, but will still get a warning.
### Create local self signed Certificate
* Use command line type, after which you will be presented with a warning that you are about to create a development certificate that is self signed, accept this.

```cmd
dotnet dev-certs https --trust
```
This can be viewed in the certificate view as a certificate named 'localhost'

## Step 2 - Create **DockerFile**
create following files in root directory
### .dockerignore 
specifies items to ignore in the image to make the image lightweight
```bash
bin/
obj/
```

### Dockerfile 
This is a multi stage build process.
* **FROM** references an image that contains everything we need to build the application

```bash
#############################################################
# STEP 1 - build the application using docker image with the
#          FULL .NET SDK
#############################################################
FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build-env
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

#############################################################
# STEP 2 - Build runtime image using only the RUNTIME
#          .net image and the SDK image is large, and not
#          required for runtime
#############################################################
FROM mcr.microsoft/dotnet/core/aspnet:3.1
WORK /app

# copy built application into folder names 'out'
COPY --from=build-env /app/out

# specify the entry point of the application when the contain runs
ENTRYPOINT "dotnet", "WeatherApi.dll"
```


## Step 3 - Create Image using **Docker Engine**
* must be in the same directory as the docker file
* *t* = tag, but be lower case
* *.* = build context, which is the current directory
```bash
docker build -t weatherapi .
```
* the build will start downloading images for the SDK and runtime
* open the Visual Studio Code Docker Plugin to see to view activity such as containers in user, and downloaded images
* after the command has run, the new weatherapi docker image will be shown
* Note that the first time this runs, docker has to download the 2 microsoft images (.Net sdk and .Net runtime), this is quite slow. The images are retained locally so the next time they are required, the containers start very quickly.





## Step 4 - Run Image in **Docker** (without HTTPS)
* *-p* map ports external and internally to the container, in the example below the container will listen to messages on port **8080** and translate them internally to the .Net application to port **80**. This overrides any settings in the **launchsettings.json**.
```bash
docker run -p 8080:80 weatherapi 
```

The endpoint can now be access in a browser without HTTPS
```
http://localhost:8080/weatherforcast
```






## Step 5 - Amend Image to run as **HTTPS**

### Create Certificate for Docker Image
* Container required a seperate certificate
* make the certificate name the same as the assembly name, including capitalization
```bash
dotnet dev-certs https -ep $env:USERPROFILE\.aspnet\https\WeatherAPI.pdf -p pa55w0rd!


## may respond with
A Valid HTTPS certificate is already present
```
you will receive a warning that the certificate exists, this is referring to your self signed development certificate, and this is not actually a duplicate.

### Trust self signed development certificates
Inform .net that we wish to trust self signed development certificates
```
dotnet dev-certs https -trust
```

### User Secrets 
User secrets are using in the development environment, they are stored as plain encrypted json in your user profile, so only you can see them.

Amend the WeatherApi.csproj file to include a user secret and create a new unique name. This name will be the name of a directory folder where the user secrets are stored.
```xml
<Project Sdk="Microsoft.Net.Sdk.Web">
    <ProjectGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretId>MySecret-C3A9B37C-12FF-4935-A7B3-C70535AD2706</UserSecretId>
    </ProjectGroup>
</Project>
```
### Create a User Secret
* you should be in the project root folder
```bash
## key is recognized by .net and used for the certificate password
dotnet user-secrets set "Kestrel:Certificates:Development:Password" "pa55w0rd!"
```
This is stored under you profile in directory /AppData/Roaming/Microsoft/UserSecrets/MySecret-C3A9B37C-12FF-4935-A7B3-C70535AD2706
the file is named secrets.json
```json
{
    "Kestrel:Certificates:Development:Password":"pa55w0rd!"
}
```
### Need to rebuild the user image as the project file has now changes
* remove the existing image using the Docker plugin (hover over the image name and right-click and select remove).. this is optional as rebuild would overwrite the image.
```
docker build -t weather .
```





  ## Step 5 - Run Image with **HTTPS**
  ### pass into docker
  * **-p** 8080:80
  * **-p** 8081:443
  * **-e** ASPNETCORE_URLS="https://+;http://+"
  * **-e** ASPNETCORE_HTTPS_PORT=8081
  * **-e** ASPNETCORE_ENVIRONMENT=Development
  * **-v** $env:APPDATA\microsoft\UserSecrets\:/root/.microsoft/usersecrets
  * **-v** $env:USERPROFILE\.aspnet\https:/root/.aspnet/https/
  * weatherapi

  the -v commands map volumes (disk directories) from the host machine to folders inside the container. The first mapping if for the user secrets that contain the password to the development certificate, and the second maps the certificate path.

```bash
## note requires powershell for use of $env local variables
docker run -p 8080:80 -p 8081:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8081 -e ASPNETCORE_ENVIRONMENT=Development -v $env:APPDATA\microsoft\UserSecrets\:/root/.microsoft/usersecrets -v $env:USERPROFILE\.aspnet\https:/root/.aspnet/https/
```
as you can see, this is a massive statement, quick clumsy and easy to introduce errors.


```bash
https://localhost:8081/WeatherApi
```

## Step 6 - Create compose file (remove the long docker run command)
Create a new file in the root directory named docker-compose.yaml

* specified services / (images or docker files)

```bash
version: '3'
services:
    weatherapi:
        build: . ## the build path is in same directory
        ports:
            - "8080" : "80"
            - "8081" : "443"
        environments:
            ASPNETCORE_URLS: "https://+;http://+"
            ASPNETCORE_HTTPS_PORT: "8081"
            ASPNETCORE_ENVIRONMENT: "Development"
        volumes:
            - ${APPDATA}\microsoft\UserSecrets\:/root/.microsoft/usersecrets
            - ${USERPROFILE}\.aspnet\https:/root/.aspnet/https/
```

to run enter the following, this actually builds an image
```bash
## to build and run
docker-compose -up --build

## to run only
docker-compose -up
```
**pressing Control+C will stop the container when you use compose.**
note that in general project a compose may utilise multiple docker images and would be placed outside the project structure.
 
# Docker Compose *(yaml)* Features
Use yaml file to specify how a docker container is launched. Specified urls 

* Reduced reliance on, and simplifies use of Docker CLI
* Allows you to start up multiple containers quickly
* Provides networking between containers
* can pass in environment variables to container


# Docker Commands
Dockers can be run and started, stopped.......???????


## ps - ??view running containers??
show instances of containers running
## stop
docker stop {containerId}
## start (start a stopped container)
docker start {containerId}
  
# References

## Docker
## How to Dockerize an ASP.Net Core application
https://docs.docker.com/engine/examples/dotnetcore/


## YouTube Videos
## Runs ASP.NET core apps in Docker with HTTPS
https://www.youtube.com/watch?v=lcaDDxJv260

* create demo
* docker overview
* Scaffold Api
* Package for Docker
* call to docker API
* Configure HTTPS
* User Secrets
* Environment Variables
* Docker Compose



## Custom HTTPS Dev Environment using .NET Core, Kestrel & certificates
https://www.youtube.com/watch?v=96KHOaIe19w

## Deploy a .NET Core API with Docker (Step-by-Step)
https://www.youtube.com/watch?v=f0lMGPB10bM

