# Docker for Vue front end applications

# Overview
Docker is a containerization platform
* can **package** (build) your application into images
* run the images as **containers** on any platform that can run **docker**
* docker image contains everything it needs to run
* docker contains (run time) are isolated by design
* Can run micro-services as containers, 
* simplifies configuration.
* Can run multiple versions of the same image


# Install View locally
Node must be installed first.
```bash
$ npm install vue
```

# Create Vue Project
Create a template vue application.
```bash
vue create dockervue
```

change directory to the application folder.
```bash
cd dockervue
```

## Run the vue project to see it running
```bash
npm run serve
```

## build the project
This will create a dist folder within the project structure with the deployable project elements.
```bash
npm run build
```

# Create a docker file
Create a new file in the application root directory named **Docker**.

You may browse search for **hub.docker** and **node** to see the latest versions.

You may browse search for **hub.docker** and **nginx** to see the latest runtime engine available.

```bash
##################################
# STEP 1 BUILD STAGE for VUE PROJECT
##################################

# get container for Node to build the application
#  note that alpine refers to a compact version of linux, 
# note **BUILD** refers an alias name container
#  node:
FROM node:12.18-1.alpine as BUILD
# create work directory
WORKDIR /app 
# copy package.json which contains all the node packages in the application
# to the working directory
COPY package.json ./
# install the packages using Node Package Manager
RUN npm install
# Copy the node modules to the working directory
COPY . .
# Build the project 
RUN npm run build

##################################
# STEP 2 Create NGINX Server
##################################

# get container for runtime, this will form the base of our image
FROM nginx:1.19-0.alpine as prod-stage
# copy built application files to the default 
# path that nginx servers by default
COPY --from=build /app/dist /usr/share/nginx/html 
# Expose port 80
EXPOSE 80
# The command to launch the nginx Runtime Engine with the container runs
# '-g' set global configuration directives
# 'daemon off' means starts the process in the foreground, this is done in
# docker containers as this is the only real process.
CMD ["nginx","-g","daemon off;"] 
```

# Build the docker image
**-t**  tag (name of the container)
**.**   reference to current folder
```bash
docker build -t dockervue .
```

list current docker images, this shows created images. there should be an entry for dockervue.
```bash
docker images
```

```bash
docker images
```


# Run the docker image
```bash
docker run -p 8080:80 dockervue
```

browse the application
```bash
http://localhost:8080
```

# References - YouTube
## Create Default Vue Application
https://www.youtube.com/watch?v=XiMBLwfMNss