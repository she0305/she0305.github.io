---
title: "Panic Attack Saving CheatSheet for Docker Beginner"
layout: splash
read_time: true
header:
  overlay_image: assets/images/dockerBanner.png
categories:
- TIL
last_modified_at: 2023-07-13
show_date: true
---

# A Survival Diary With Docker (Can't avoid it anymore)

As I mentioned in several previous posts, I've been trying my best to avoid learning Docker. I watched a few videos and read a few articles about Docker, but I never really understood what it was. I was able to get away with it for a while, but I finally had to face it. I had to learn Docker. 

I am still not an expert, but I am writing this post to convince anyone who is intimidated by Docker (just like me) that it is actually so worth learning. To be honest, I panicked, internally 😱, every time I realized that I had to go more in depth with Docker, but learning Docker turned out to be not bad at all! I hope this post can save you from unnecessary anxiety that I had to go through.

This post is not about explaining what Docker is or how to use it. I will be simply sharing the potential headache that you are facing right now and the steps/commands that Docker can resolve. I will start in the more manual way by using multiple docker commands and gradually move on to docker compose to simplify the process.

## Terminology
- Image: A template for creating a container
  - Image is a read-only template with instructions for creating a Docker container
  - `docker build` command creates an image from a Dockerfile
  - If pre-built image is not available, you can build your own image using Dockerfile.
  - It does not run by itself. 
  - It can be used as a container.
- Container: A running instance of an image
  - `docker run` command creates a container from an image
  - Multiple containers can be created from a single image (all isolated from each other)
  - It's a read-write layer on top of the image
  

## Docker Commands & Flags You Will Use Frequently
```bash
--name NAMEb# Assign a name to the container
-t NAME:TAG # Tag an image
-d # Run container in background and print container ID
-p # Publish a container's port(s) to the host
-v # Bind mount a volume
--rm # Automatically remove the container when it exits
-it # Interactive mode (CTRL+C to exit)
-f # Force the removal of a running container
-e # Set environment variables
```

```bash
docker build . # Build image from Dockerfile in the current directory
docker ps # List all running containers ps->process status
docker images # List all images (locally stored)
docker run IMAGE # Create/start a container from an image
docker rmi IMAGE # Remove an image
docker rm CONTAINER # Remove a container
docker stop CONTAINER # Stop a container
docker start CONTAINER # Start a container
docker container prune # Remove all stopped containers
docker image prune # Remove all dangling images
docker compose up # Create and start containers
docker compose down # Stop and remove containers, networks, images, and volumes
```

## 1. [Facing the Needs of Docker] Annoying Environment Setup -> Get Started With Docker 

Downloading and setting up environment on different laptops became way too annoying. I have gone through multiple formatting and reinstalling of my OS and computers, and I had to set up PostgreSQL every single time. Finally, my patience ran out, and I decided to use Docker for my new project that requires PostgreSQL. As I described in [PostgreSQL alone or with Docker?](https://she0305.github.io/til/TIL-backend/) post, setting up PostgreSQL without Docker requires a lot of manual work. I had to install PostgreSQL, create a database, create a user, and grant privileges to the user. I had to do all of this manually, and I had to do it every time I wanted to start a new project.

**😰 Headache: Setting Environment**
- Setting up effort = Whatever that requires you to set up your environment manually x every machine that needs to run the project
- 

**😌 All You Need To Do** (If you use Docker)
- (Database Setup) docker run database image (e.g. postgres)
- (Backend/Frontend Setup) Write a Dockerfile to build your own image
- Run docker commands to run the containers

For the database, we can use the pre-built image from Docker Hub. For the backend and frontend, we can write a Dockerfile to build our own image.
```bash
# Creating a container for PostgreSQL
# Automatically pull the database image (e.g. postgres) from Docker Hub if you don't have it
docker run postgres  
# Run image as container (Make sure to update your .env file)
docker run -d -e POSTGRES_DB=<container_name> -e POSTGRES_PASSWORD=<password> -e POSTGRES_USER=<postgres_user> -p <port_number>:5432 -d postgres
```

Ta-da! You have a containerized environment (such as PostgreSQL database) that you can bring up by simple docker commands.

For frontend and backend, you can write a Dockerfile to build your own image. 
```bash
# Dockerfile Example
# Get the version of Node.js that you need
FROM node:18

# Create app directory (in the container)
WORKDIR /<yourProject>/frontend

# Install app dependencies (in the container)
COPY package.json yarn.lock ./

# Install dependencies
RUN yarn 

# Copy app source code (in the container)
COPY . .

# Port that other ends run (this doesn't do anything but just for documentation)
EXPOSE 3000

# Run the app (It's executed when a container is created/started from this image, not when the image is built)
CMD ["yarn", "dev"]
```

Now you can run the following commands to build an image from Dockerfile and run a container from the image. (Need to run the commands in the same directory as Dockerfile) Also, make sure to run the backend container after the database container is up and running. Then run the frontend container after the backend container is up and running.
```bash
# Build an image from Dockerfile
docker build -t <image_name>:<tag> .
# Run a container from the image
docker run -d -p <port_number>:3000 <image_name>:<tag>
```

## 2. [Too Many Commands] Too Many Commands -> Docker Compose

As you can see, there are too many commands to run to bring up the environment. You need to remember to build an image from Dockerfile and run a container from the image. You also need to remember to run the database container first, then the backend container, and then the frontend container. If you need to work with multiple projects, it will be a nightmare to run containers for each project.

**😰 Headache: Too Many Commands**
- Remembering commands = Number of commands for each container x Number of containers x Number of projects
- Remembering the order of commands

**😌 All You Need To Do** (If you use Docker Compose)
- Write a docker-compose.yml file
- Run `docker compose up` command

Docker Compose is a tool for defining and running multi-container Docker applications. You can use a YAML file to configure your application's services. Then, with a single command, you can create and start all the services from your configuration. Also, docker compose creates a network for your services and attaches the containers to the network. This allows the containers to communicate with each other without the need for exposing ports on the host machine.

```bash
# docker-compose.yml Example
# Docker Compose Version
version: "3.8"
services:
  # Containers
  # You can name the containers
  # By default when you use docker compose, when you bring your services down, the containers are removed
  frontend:
    build:
      context: ./frontend # The directory where the Dockerfile is located
      dockerfile: Dockerfile # You can specify the name of the Dockerfile if you have multiple Dockerfiles
    depends_on:
      - postgres # The frontend container will be started after the postgres container is up and running
    environment:
      #If you have key value pairs, you don't need dash as it creates yaml objects
      DATABASE_URL: postgres://postgres:postgres@postgres:5432/local # postgres://<user>:<password>@<host>:<port>/<database>
      SECRET: only-for-dev 
      GOOGLE_ID: ${GOOGLE_ID} # You can use environment variables in docker-compose.yml file
      GOOGLE_SECRET: ${GOOGLE_SECRET}
      NEXTAUTH_URL: http://localhost:3000/
    ports:
      - "3000:3000" # "Local port:Container port"
    volumes:
      - ./frontend/:/frontend/:ro # Mounting the local directory to the container directory. For frontend, we will make it read-only

    # Everything in this docker compose file will be in the same network by default. Docker will automatically create a new environment for all the services specified here and add them to the network

  postgres:
    # image: postgres:13.1-alpine is referring to the version of Postgres, alpine is the smaller subset of Linux, more secure, but a lot less functionality than a full Linux docker image
    image: postgres:14
    environment:
      POSTGRES_DB: local
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    volumes:
        - ./postgres_data/:/var/lib/postgresql/data #this is where the data is stored in the container (in the postgres container) and we are mounting it to the local directory. It will be read-write by default
```
Now, your docker-compose.yml file is ready. All you need to do is run one command to bring up the environment. Your containers will be up and running in the order you specified in the docker-compose.yml file.

```bash 
docker compose up
```

