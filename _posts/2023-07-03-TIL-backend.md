---
title: "PostgreSQL alone or with Docker?" 
layout: splash
read_time: true
header:
  overlay_image: assets/images/DockerPostgres.jpeg
categories:
- TIL
last_modified_at: 2023-07-3
---

# Learning By Doing: Docker or no Docker for PostgreSQL?

I've seen a lot of people using Docker for their PostgreSQL database. So I thought about it for a second whether I should use Docker for my PostgreSQL database or not. Unfortunately, my lazy self decided not to use Docker, as Docker has always been something I've been intimidated by. Long story short, I started with setting my PostgreSQL database up without Docker AND ended up scrapping the whole thing and starting over with Docker.

## Why I decided to use Docker
The main reason why I decided to use Docker is that running PostgreSQL requires a lot more manual work than I thought. I had to install PostgreSQL, create a database, create a user, and grant privileges to the user. I had to do all of this manually, and I had to do it every time I wanted to start a new project. 

Aside from the manual work, I found that having Docker offers a lot more advantages. Obviously, Docker is a containerization platform that allows you to run your application in an isolated environment. This means that you can run your application in the same environment regardless of the machine you are using. This is a huge advantage for me as I'm actively developing on two different machines (Mac and Linux). Security is another advantage of using Docker. Docker isolates your application from the host machine, which means that if your application is compromised, your host machine will be safe. Furthermore, Docker enables horizontal scaling by running multiple containers if my application becomes popular.

Well, the good thing is that now I know how to set up PostgreSQL both with and without Docker. In this post, I will share both ways of setting up PostgreSQL.

## PostgreSQL without Docker
### Install PostgreSQL
First, you need to install PostgreSQL. You can follow the [official documentation](https://www.postgresql.org/download/) to install PostgreSQL. 

### Create a database
Once you have PostgreSQL installed, you can access the PostgreSQL shell by running the following command in your terminal. 

```bash
psql postgres
``` 

Now that you are in the PostgreSQL shell, you can create a user and password by running the following command. 

```bash
CREATE USER <username> WITH PASSWORD '<password>';
```

Then, you need to grant privileges to the user you just created. 

```bash
ALTER ROLE <username> WITH SUPERUSER;
``` 

Now, you can create a database by running the following command. 

```bash
CREATE DATABASE <database_name>;
```

Here are some useful commands you can use in the PostgreSQL shell. 

```bash
# List all databases
\l
# Quit the shell
\q
# Connect to a database
\c <database_name>
# List all tables in the database
\dt
```

Now, you can open your `.env` file and update the `DATABASE_URL` with the following information. 

```bash
DATABASE_URL=postgresql://<username>:<password>@localhost:5432/<database_name>
```

Make sure to restart your server after updating the `DATABASE_URL` in your `.env` file and you have the port `5432` available.

## PostgreSQL with Docker
### Install Docker
First, you need to install Docker. You can follow the [official documentation](https://docs.docker.com/get-docker/) to install Docker.

### Pull the PostgreSQL image
Once you have Docker installed, you can pull the PostgreSQL image by running the following command. 

```bash
docker pull postgres
```

### Run the PostgreSQL container 
Now, you can run the PostgreSQL container by running the following command. 

```bash
docker run -d -e POSTGRES_NAME=<container_name> -e POSTGRES_PASSWORD=<password> -e POSTGRES_USER=<postgres_user> -p 5432:5432 -d postgres
```
If you have a port conflict, you can change the port number on the left side of the colon. 

```bash
docker run -d -e POSTGRES_DB=<container_name> -e POSTGRES_PASSWORD=<password> -e POSTGRES_USER=<postgres_user> -p <port_number>:5432 -d postgres
```

### Update the DATABASE_URL
Now, you can open your `.env` file and update the `DATABASE_URL` with the following information. 

```bash
DATABASE_URL=postgresql://<postgres_user>:<password>@localhost:<port_number>/<container_name>
```

