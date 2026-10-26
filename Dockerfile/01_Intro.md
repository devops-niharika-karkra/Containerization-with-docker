# 01 — Introduction to Dockerfiles

## What Is a Dockerfile?

A Dockerfile is a simple text file that contains a set of instructions Docker follows to build an image for an application.

It defines everything needed to package an application, including the base image, application files, dependencies, environment settings, and commands to run when a container starts.

Think of a Dockerfile as a recipe that tells Docker how to prepare your application so it can run consistently across different environments.

## Dockerfile vs Image vs Container vs Registry

These four terms are closely related, but each has a different purpose.

- **Dockerfile:** Contains the instructions Docker uses to build an image.
- **Image:** A packaged version of an application along with everything it needs to run.
- **Container:** A running or stopped instance created from an image.
- **Registry:** A place where Docker images are stored and shared, such as Docker Hub or Amazon ECR.

## Why Do We Use Dockerfiles?

Dockerfiles make application packaging easier, more reliable, and repeatable. Here are some important benefits:

- **Automation:** Build images automatically instead of configuring everything manually.
- **Consistency:** Run applications in a similar environment on a developer's laptop, a testing server, or a production system.
- **Version control:** Store Dockerfiles in GitHub and track changes to your application setup.
- **Repeatability:** Rebuild images whenever needed using the same set of instructions.
- **Simplified deployment:** Package the application and its dependencies so it is easier to deploy across different environments.

## Basic Dockerfile Workflow

The process of creating and running a container usually follows these steps:

1. **Write a Dockerfile:** Define the base image, dependencies, application files, and startup instructions.
2. **Build an image:** Use the Dockerfile to create an image.
3. **Run a container:** Create a container from the image.
4. **Test the application:** Check whether the application is running and working as expected.
5. **Push the image to a registry:** Upload the image to a registry when you need to share it or deploy it on another system.

## Important Docker Commands

```bash
# Check the installed Docker version
docker --version

# Build an image from the Dockerfile in the current directory
docker build -t my-image:v1 .

# List available images
docker images

# Create and start a container
docker run --name my-container my-image:v1

# List all containers, including stopped ones
docker ps -a
```

**Note:** The `.` at the end of the `docker build` command tells Docker to use the current directory as the build context.

## Practice Exercise

Try answering these questions to check your understanding:

1. What is a Dockerfile, and why do we need it?
2. What is the difference between a Dockerfile, an image, and a container?
3. What is the purpose of a Docker registry?
4. Draw a simple diagram showing how source code is packaged into an image and then used to run an application inside a container.

## Key Takeaway

A Dockerfile provides a repeatable way to package an application and its dependencies into a Docker image. That image can then be used to create containers, helping applications run consistently across different environments.
