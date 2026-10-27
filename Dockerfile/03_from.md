# 03 — FROM Instruction

## What Is FROM?

The `FROM` instruction tells Docker which base image to use when building a new image.

Think of it as the **starting point of your Docker image**. Instead of creating everything from scratch, you can use an existing image that already contains an operating system, a programming language, or a web server.

The `FROM` instruction is usually the first instruction in a Dockerfile.

## Syntax

```dockerfile
FROM image-name:tag
```

Here:
- **Image name:** Specifies the image you want to use.
- **Tag:** Specifies the version or variant of that image.

## Examples

### 1. Using Ubuntu

```dockerfile
FROM ubuntu:24.04
```

This uses Ubuntu 24.04 as the base image. You can then add packages, configure settings, and install the tools your application needs.

### 2. Using Apache HTTP Server

```dockerfile
FROM httpd:2.4
```

This uses an Apache HTTP Server image, which is useful when you want to create a container that serves web content.

### 3. Using Python

```dockerfile
FROM python:3.12-slim
```

This uses a Python 3.12 image with a relatively minimal base, helping reduce unnecessary files and image size.

## Why Is FROM Important?

Every Docker image needs a starting point. The `FROM` instruction provides that foundation.

Choosing the right base image can help you:
- **Save time:** Use an existing environment instead of setting everything up manually.
- **Maintain consistency:** Build images using a known software environment.
- **Reduce image size:** Choose a lightweight image when appropriate.
- **Simplify deployment:** Package the software and dependencies your application needs.
- **Improve security:** Start with a trusted, maintained image and keep it updated.

**Remember:** Your choice of base image depends on what you want your container to do.

## Practical Lab

Let's create a simple Docker image using Ubuntu.

### Step 1: Create a Dockerfile

Create a file named `Dockerfile` and add the following instructions:

```dockerfile
FROM ubuntu:24.04

CMD ["cat", "/etc/os-release"]
```

### Step 2: Understand the Dockerfile

- `FROM ubuntu:24.04` — Uses Ubuntu 24.04 as the base image.
- `CMD ["cat", "/etc/os-release"]` — Runs a command that displays information about the operating system when the container starts.

### Step 3: Build the Docker Image

Run the following command in the directory containing your Dockerfile:

```bash
docker build -t ubuntu-base:v1 .
```

Let's break it down:

- `docker build` — Builds an image from the Dockerfile.
- `-t ubuntu-base:v1` — Assigns the image the name `ubuntu-base` and tag `v1`.
- `.` — Tells Docker to use the current directory as the build context.

### Step 4: Run the Container

```bash
docker run --rm ubuntu-base:v1
```

This command creates and starts a container from your image. It displays the Ubuntu operating system information and removes the container after it exits.

**Expected result:** You should see details about the operating system, including its name, version, and other release information.

## Important Things to Remember

- Always choose a trusted and maintained base image.
- Use an appropriate version tag instead of relying on an unclear or changing version.
- For stricter reproducibility, consider pinning the base image by its digest.
- A smaller image can reduce download and storage requirements, but smaller does not automatically mean more secure.
- Select a base image according to your application's requirements.

## Practice Tasks

Test your understanding by completing these tasks:

1. Create a Docker image using `ubuntu:24.04`.
2. Create another Docker image using `httpd:2.4`.
3. Compare the two base images. What software and functionality does each provide?
4. Research `FROM scratch`. How is it different from using a full operating-system image such as Ubuntu?
5. Experiment with different base images and observe how they affect the image size and available tools.

## Key Takeaway

**The `FROM` instruction defines the foundation of your Docker image.** Choosing the right base image makes it easier to build consistent, efficient, and maintainable containers.
