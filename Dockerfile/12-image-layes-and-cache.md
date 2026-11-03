# 12 — Image Layers and Build Cache

## 1. What Are Docker Image Layers?

Docker images are built using multiple filesystem layers along with image configuration.

Think of a Docker image like a cake made up of different layers. Each layer adds something to the final image.

For example:

- One layer provides the base operating system.
- Another layer installs required packages.
- Another layer adds application files.
- The final configuration defines how the container should start.

Many Dockerfile instructions, such as `RUN`, `COPY`, and `ADD`, create filesystem changes that can be stored as image layers.

### Why Are Layers Important?

Docker layers provide several benefits:

1. **Reusability:** Multiple images can share the same layers.
2. **Storage efficiency:** Docker does not need to store duplicate copies of identical layers.
3. **Faster builds:** Previously created layers can be reused when the build cache is valid.
4. **Efficient distribution:** Docker can transfer only the layers that are missing on the destination system.

**Remember:** Docker images are made up of reusable layers, but not every Dockerfile instruction creates a separate filesystem layer.

## 2. What Is Docker Build Cache?

Docker Build Cache is a mechanism that helps Docker avoid repeating work during image builds.

When you build an image, Docker checks whether it can reuse the result of a previous build step.

If the instruction and its relevant inputs have not changed, Docker may reuse the cached result instead of executing that step again.

### Understand It With a Simple Example

Imagine you are building an application.

The first time you build the image:

- Docker downloads the base image if required.
- Docker installs the required packages.
- Docker copies your application files.
- Docker creates the final image.

Now, suppose you build the same image again without changing anything.

Docker can reuse the previous build results instead of repeating all the work.

This saves time, especially when your application has many dependencies.

## 3. Example Dockerfile

Let's create a Dockerfile to understand image layers and build caching.

```dockerfile
FROM ubuntu:24.04

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY app.sh .

CMD ["bash", "app.sh"]
```

### Understanding Each Instruction

| Instruction | Purpose |
|---|---|
| `FROM` | Selects the base image |
| `RUN` | Installs curl and removes unnecessary package lists |
| `WORKDIR` | Sets `/app` as the working directory |
| `COPY` | Copies the application script into the image |
| `CMD` | Defines the command to run when the container starts |

## 4. Practical Lab — Understanding Build Cache

Let's perform a practical exercise.

### Step 1: Create a Project Directory

```bash
mkdir cache-demo
cd cache-demo
```

### Step 2: Create an Application File

```bash
cat > app.sh <<EOF
#!/bin/bash
echo "Hello from Docker Build Cache!"
EOF
```

### Step 3: Create the Dockerfile

```dockerfile
FROM ubuntu:24.04

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY app.sh .

CMD ["bash", "app.sh"]
```

### Step 4: Build the Image for the First Time

```bash
docker build --progress=plain -t cache-demo:v1 .
```

During the first build, Docker needs to perform the required build steps.

### Step 5: Build the Same Image Again

```bash
docker build --progress=plain -t cache-demo:v1 .
```

Observe the build output.

You may see messages such as:

```text
CACHED
```

This means Docker reused the result of a previous build step instead of executing it again.

**Expected result:** If nothing relevant has changed, Docker can reuse most or all of the cached build steps.

## 5. Practical Experiment — What Happens When a File Changes?

Now let's understand how Docker decides whether to reuse the cache.

### Step 1: Change the Application File

```bash
cat > app.sh <<EOF
#!/bin/bash
echo "Application updated!"
EOF
```

### Step 2: Build the Image Again

```bash
docker build --progress=plain -t cache-demo:v1 .
```

### What Will Happen?

Docker checks the build steps again.

- `FROM` may be reused.
- The package installation step may be reused.
- `WORKDIR` may be reused.
- `COPY app.sh .` will need to run again because the file has changed.
- `CMD` may be reused if its instruction and relevant inputs remain unchanged.

This demonstrates an important concept:

**Changing one file does not necessarily mean Docker has to rebuild the entire image from the beginning.**

Docker can reuse earlier valid cache results and rebuild the affected steps.

## 6. Inspect Docker Image History

Docker provides a command to view the history of an image.

```bash
docker history cache-demo:v1
```

This command displays information about the image's build history, including:

- Image layers and their associated commands.
- The size associated with each history entry.
- The instructions used to build the image.

**Note:** Image history is useful for understanding how an image was built, but it does not show whether a particular build step was cached. Use `docker build --progress=plain` to observe cache reuse during a build.

## 7. Best Practices for Image Layers and Build Cache

Follow these practices when writing Dockerfiles:

1. **Place stable instructions first:** Keep dependency installation steps before frequently changing application files when practical.
2. **Combine related commands:** Combine package update and installation commands using `&&` to avoid unnecessary intermediate layers.
3. **Remove unnecessary files:** Clean package lists and temporary files to reduce image size.
4. **Avoid unnecessary cache invalidation:** Do not change dependency-installation instructions unless required.
5. **Use `.dockerignore`:** Exclude unnecessary files from the build context so they do not affect builds.
6. **Understand cache dependencies:** Changes to an instruction or its relevant inputs can invalidate that step and subsequent dependent steps.

## 8. Why Is Build Cache Important in DevOps?

Build caching is especially useful in CI/CD pipelines.

Imagine a company builds Docker images every time a developer pushes new code to GitHub.

Without effective caching, Docker may repeatedly install the same dependencies during every build.

With build caching:

- Unchanged dependencies can be reused.
- Image builds can become faster.
- CI/CD pipeline execution time can be reduced.
- Build resources can be used more efficiently.

This is why understanding Docker build cache is important for DevOps engineers.

## 9. Practice Tasks

Try completing these tasks yourself:

1. Create a Dockerfile using Ubuntu as the base image.
2. Install curl using a `RUN` instruction.
3. Build the image twice and observe the output.
4. Change only the application file and rebuild.
5. Identify which steps were cached and which steps ran again.
6. Inspect the image using `docker history`.
7. Explain why Docker does not need to rebuild every step when only one application file changes.
8. Explain how build caching improves CI/CD pipeline performance.

## 10. Key Takeaway

Docker image layers help organize and reuse filesystem changes, while Docker build cache helps avoid repeating work during image builds.

Both concepts improve build efficiency and help reduce unnecessary storage and processing.

**Remember:**

- **Image Layers:** Represent filesystem changes that make up an image.
- **Build Cache:** Reuses previous build results when the relevant inputs and instructions have not changed.

## 11. Quick Revision

| Concept | Meaning |
|---|---|
| Image Layers | Filesystem changes that make up a Docker image |
| Build Cache | Reuses previous build results |
| `CACHED` | Indicates that Docker reused a cached build result |
| `docker build` | Builds a Docker image |
| `--progress=plain` | Displays detailed build output |
| `docker history` | Displays an image's build history |
| Cache Invalidation | Happens when a build step's relevant inputs or instructions change |

**DevOps Interview Tip:** Be prepared to explain how Docker build caching works, why changing an application file does not always require rebuilding dependencies, and how you would optimize a Dockerfile to make CI/CD builds faster.
