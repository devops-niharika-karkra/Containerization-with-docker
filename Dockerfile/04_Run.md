# 04 — RUN Instruction Of Dockerfile

## What Is RUN?

The `RUN` instruction executes a command **while Docker is building an image**.

It is commonly used to install software, create directories, configure the environment, and prepare everything an application needs to run.

Any changes made to the image's filesystem by a `RUN` command are saved in the resulting image.

**In simple words:** `RUN` prepares your image by executing commands during the build process.

## Syntax

```dockerfile
RUN command
```

## Example

Let's create a Docker image using Ubuntu and install `curl` inside it.

```dockerfile
FROM ubuntu:24.04

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*

CMD ["curl", "--version"]
```

### Understanding the Dockerfile

Let's understand what each instruction does.

- `FROM ubuntu:24.04` — Uses Ubuntu 24.04 as the base image.
- `RUN apt-get update` — Updates the package information so Ubuntu can identify the available software packages.
- `apt-get install -y --no-install-recommends curl` — Installs `curl` without prompting for confirmation and avoids installing recommended packages that are not required.
- `rm -rf /var/lib/apt/lists/*` — Removes the downloaded package-list files to help reduce the final image size.
- `CMD ["curl", "--version"]` — Runs a command to display the installed version of `curl` when the container starts.

**Why are multiple commands combined using `&&`?**

The `&&` operator executes the next command only if the previous command succeeds. This helps prevent the installation and cleanup steps from continuing after an earlier failure.

The backslash (`\`) allows us to split a long command across multiple lines, making the Dockerfile easier to read.

## Build and Run

### Step 1: Build the Docker Image

Save the instructions in a file named `Dockerfile`, then run:

```bash
docker build -t run-demo:v1 .
```

This command builds the image and assigns it the name `run-demo` with the tag `v1`.

During the build, Docker executes the `RUN` instruction and installs `curl` into the image.

### Step 2: Run the Container

```bash
docker run --rm run-demo:v1
```

This starts a container from the newly built image.

The `CMD` instruction executes `curl --version`, and you should see the installed version of `curl` in the output.

The `--rm` option automatically removes the container after it exits.

## Why Do We Use RUN?

The `RUN` instruction helps us prepare an image before creating a container.

Some common uses include:

- **Installing software:** Install tools such as `curl`, `vim`, or `git`.
- **Creating directories:** Prepare application directories such as `/app` or `/var/www/html`.
- **Installing dependencies:** Set up the libraries and packages required by an application.
- **Configuring the environment:** Modify files or set up the filesystem.
- **Managing permissions:** Change file ownership or permissions when required.

## Best Practices

When using `RUN`, keep these points in mind:

- **Combine related commands:** Use `&&` to connect commands that logically belong together.
- **Clean up unnecessary files:** Remove package caches and temporary files when appropriate.
- **Avoid unnecessary packages:** Install only what your application needs.
- **Never store secrets in image layers:** Passwords, API keys, and access tokens should not be included in Docker image instructions or build layers.
- **Understand when RUN executes:** It runs during the image build, not every time a container starts.
- **Keep your Dockerfile readable:** Use line breaks and clear command structures for easier maintenance.

## Practice Tasks

Test your understanding by completing the following tasks:

1. Create a Docker image using `ubuntu:24.04` and install `curl` using the `RUN` instruction.
2. Modify your Dockerfile to create a directory named `/app`.
3. Build the image and run a container to verify that the `/app` directory exists.
4. Install an additional package and check whether it is available inside the container.
5. Compare the image sizes of two images: one containing only the required packages and another containing unnecessary packages.

## Key Takeaway

**The `RUN` instruction prepares your Docker image by executing commands during the build process.**

For example, if your application requires `curl`, you can install it using `RUN` while building the image. Every container created from that image can then use the installed software without needing to install it again at startup.
