# Docker Guide

Why use Docker? — For team collaboration.  
Then how should we use Docker?

## Personal Projects

### Concept Overview

When working on a personal project, the typical workflow often looks like this:

![image](https://github.com/user-attachments/assets/2b3c5b44-d711-4968-966f-0958cd960b7c)

We write code in our **local environment** and run it to observe the output or debug.

So what’s the problem with this approach? In a team setting, every developer might have a different local setup.

This is exactly where Docker comes into play. The Docker-based workflow looks more like this (note that the code remains the same):

![image](https://github.com/user-attachments/assets/7acffeab-04d4-4fa3-bf6a-80f5a98231a6)

All you need to do is write a **Dockerfile** that tells Docker what dependencies your program needs. Docker will then automatically build a consistent runtime environment (a **container image**) for you.  
(This environment is much more lightweight than a full virtual machine.)

With Docker, you can now safely share and collaborate on your code with others—without worrying about the “it works on my machine” issue.

### Getting Started

To Dockerize your project, the first step is to create a `Dockerfile`.  
(If you're not responsible for initializing Docker in the project, you can skip this step.)

Suppose your project has the following structure:

```bash
my-app/
├── app/
│   └── main.py
├── requirements.txt
└── Dockerfile
```

A basic `Dockerfile` might look like this:

```Dockerfile
# Use the official Python image as base
FROM python:3.11

# Set the working directory inside the container
WORKDIR /app

# Copy the project files into the container
COPY . .

# Install dependencies
RUN pip install -r requirements.txt

# Run the application
CMD ["python", "app/main.py"]
```

For more details, refer to the [[Dockerfile reference](https://docs.docker.com/reference/dockerfile/)](https://docs.docker.com/reference/dockerfile/).

Now use the following command to build the image:

```bash
docker build -t <image-name> .
```

After building the image, create a container using:

```bash
docker run --name <container-name> <image-name>
```

From now on, you can start the container anytime using:

```bash
docker start <container-name>
```

This is equivalent to running your code locally. Let’s revisit the diagram:

![image](https://github.com/user-attachments/assets/7acffeab-04d4-4fa3-bf6a-80f5a98231a6)

As long as your dependencies remain unchanged (i.e., both `Dockerfile` and `requirements.txt` stay the same), you can use `docker start <container-name>` to run your code anytime.  
If your dependencies change, however, you'll need to rebuild the image using `docker build`.

---

## Full-Stack Projects — Docker Compose

Here's an example structure for a project that includes both frontend and backend:

```bash
my-project/
├── frontend/
│   ├── Dockerfile
│   └── app/
├── backend/
│   ├── Dockerfile
│   └── src/
└── docker-compose.yml
```

Both the frontend and backend directories already have their own `Dockerfile`.

If you want to run both parts together—for integration or full project testing—you’ll need to use Docker Compose.

First, create a `docker-compose.yml` file like the one below:

```yaml
version: '3'
services:
  frontend:
    build:
      context: ./frontend
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/app
    networks:
      - app-net

  backend:
    build:
      context: ./backend
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/app
    networks:
      - app-net

networks:
  app-net:
```

For more details, check out the [[Compose file reference](https://docs.docker.com/reference/compose-file/)](https://docs.docker.com/reference/compose-file/).

---

### Workflow

First-time setup:

```bash
docker-compose up --build
```

For subsequent runs, as long as your dependencies haven’t changed:

```bash
docker-compose up
```

To stop all containers:

```bash
docker-compose down
```

This will spin up both the frontend and backend containers, and put them on the same custom network, allowing them to communicate with each other.

---

At this point, you've successfully set up your project to run in Docker.

