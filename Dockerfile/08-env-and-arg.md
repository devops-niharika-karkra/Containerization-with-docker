# 08 — ENV and ARG Instructions

## What Is ENV?

The `ENV` instruction is used to define environment variables inside a Docker image.

Environment variables store values that an application or process can use, such as the application environment, configuration settings, or a default port number.

**In simple words:** `ENV` lets you store a value in your Docker image so that your application can use it when the container runs.

## Example: Using ENV

```dockerfile
FROM ubuntu:24.04

ENV APP_ENV=development

CMD ["sh", "-c", "echo $APP_ENV"]
```

### Understanding the Dockerfile

- `FROM ubuntu:24.04` — Uses Ubuntu 24.04 as the base image.
- `ENV APP_ENV=development` — Creates an environment variable named `APP_ENV` and assigns it the value `development`.
- `CMD ["sh", "-c", "echo $APP_ENV"]` — Starts a shell that reads the value of `APP_ENV` and displays it.

The `sh -c` command allows the shell to expand the `$APP_ENV` variable before executing the command.

### Build and Run

**Step 1: Build the Docker image**

```bash
docker build -t env-demo .
```

**Step 2: Run the container**

```bash
docker run --rm env-demo
```

**Expected output:**

```text
development
```

The output shows the value assigned to `APP_ENV` in the Dockerfile.

### Override the ENV Value

You can override an environment variable when starting a container by using the `-e` option.

Run:

```bash
docker run --rm -e APP_ENV=production env-demo
```

**Expected output:**

```text
production
```

Although the Dockerfile defines `APP_ENV` as `development`, Docker uses `production` for this container because you supplied a different value at runtime.

**Important:** This changes the environment variable for the running container. It does not modify the original Docker image.

---

## What Is ARG?

The `ARG` instruction defines a variable that can be used during the Docker image build process.

It is useful when you want to pass values into a build without permanently hardcoding them into every instruction.

For example, you might use `ARG` to specify an application version or select a build configuration.

**In simple words:** `ARG` allows you to pass values to Docker while building an image.

## Example: Using ARG

```dockerfile
FROM ubuntu:24.04

ARG APP_VERSION=1.0

RUN echo "Building version ${APP_VERSION}"

CMD ["echo", "Build complete"]
```

### Understanding the Dockerfile

- `FROM ubuntu:24.04` — Uses Ubuntu 24.04 as the base image.
- `ARG APP_VERSION=1.0` — Defines a build argument named `APP_VERSION` with a default value of `1.0`.
- `RUN echo "Building version ${APP_VERSION}"` — Displays the application version during the image build.
- `CMD ["echo", "Build complete"]` — Displays a message when a container starts.

In this example, `APP_VERSION` is used during the build process.

### Build the Image

Run:

```bash
docker build -t arg-demo .
```

During the build, Docker uses the default value `1.0`.

You should see the build output containing a message similar to:

```text
Building version 1.0
```

Depending on Docker's build cache, the command's output may be reported as a cached step.

### Pass a Different ARG Value

Suppose you want to build the image using version `2.0` instead of `1.0`.

Run:

```bash
docker build --build-arg APP_VERSION=2.0 -t arg-demo .
```

Here:
- `--build-arg` passes a value to a build argument.
- `APP_VERSION=2.0` sets the argument to `2.0` for this build.
- `-t arg-demo` assigns the image the name `arg-demo`.

The `RUN` instruction will now use the value `2.0`.

**Remember:** `ARG` is primarily intended for build-time configuration. Its value is not automatically available as an environment variable when the container runs.

---

## ENV vs ARG

Both instructions allow you to define variables in a Dockerfile, but they serve different purposes.

| Feature | ENV | ARG |
|---|---|---|
| Primary purpose | Configure environment variables | Provide build-time variables |
| Defined using | `ENV` | `ARG` |
| Set a value during the build | Yes | Yes, using `--build-arg` |
| Available to the running container by default | Yes | No |
| Can have a default value | Yes | Yes |
| Common use | Application configuration | Build versions and build options |
| Suitable for storing secrets | No | No |

### When Should You Use ENV?

Use `ENV` when a value needs to be available to processes inside the container by default.

For example:

```dockerfile
ENV APP_ENV=development
```

This is useful when an application needs to know which environment it is running in.

Other examples include:
- Application configuration.
- Default runtime settings.
- Environment variables used by application processes.

### When Should You Use ARG?

Use `ARG` when a value is needed during the image build.

For example:

```dockerfile
ARG APP_VERSION=1.0
```

You can override it when building the image:

```bash
docker build --build-arg APP_VERSION=2.0 -t my-app:v2 .
```

This is useful when you want to customize a build without editing the Dockerfile each time.

## Important Concept: Using ARG and ENV Together

Sometimes, you may want to accept a value during the build and make it available to the running container.

You can do this by using both instructions.

### Example

```dockerfile
FROM ubuntu:24.04

ARG APP_VERSION=1.0

ENV APP_VERSION=${APP_VERSION}

CMD ["sh", "-c", "echo Application version: $APP_VERSION"]
```

### How It Works

1. `ARG APP_VERSION=1.0` defines a build-time variable.
2. `ENV APP_VERSION=${APP_VERSION}` assigns the build argument's value to an environment variable.
3. `CMD` displays the environment variable when the container starts.

Build the image with a custom
