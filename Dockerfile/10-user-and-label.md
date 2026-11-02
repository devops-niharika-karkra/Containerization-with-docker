# 10 — USER and LABEL Instructions

## What Is USER?

The `USER` instruction specifies which user and, optionally, which group should be used for subsequent instructions in a Dockerfile and for the container's runtime process.

By default, processes in many Docker images run as the `root` user unless a different user is configured.

The `USER` instruction allows you to switch to another user, such as an application-specific user, to reduce unnecessary root access.

**In simple words:** `USER` tells Docker which user should execute commands and run the application.

## Example: Running a Container as a Non-Root User

```dockerfile
FROM ubuntu:24.04

RUN useradd --create-home appuser

USER appuser

CMD ["id"]
```

### Understanding the Dockerfile

- `FROM ubuntu:24.04` — Uses Ubuntu 24.04 as the base image.
- `RUN useradd --create-home appuser` — Creates a user named `appuser` and a home directory for that user.
- `USER appuser` — Switches to `appuser` for subsequent Dockerfile instructions and the container's runtime process.
- `CMD ["id"]` — Displays information about the current user when the container starts.

The `id` command displays information such as the user's ID (UID) and group ID (GID).

### Build and Run

**Step 1: Build the Docker image**

```bash
docker build -t user-demo .
```

**Step 2: Run the container**

```bash
docker run --rm user-demo
```

### Expected Output

The output should look similar to this:

```text
uid=1000(appuser) gid=1000(appuser) groups=1000(appuser)
```

The exact UID and GID may vary depending on the image and how the user was created.

The important point is that the output identifies `appuser` rather than `root`.

## Why Do We Use USER?

Running applications as a non-root user is an important security practice in Docker.

The root user has extensive privileges inside the container. If an application running with those privileges is compromised, the potential impact can be greater.

Using a dedicated, non-root user helps limit unnecessary privileges.

Some important benefits include:

- **Improved security:** Reduces the privileges available to the application.
- **Reduced risk:** Limits the potential impact of certain application vulnerabilities.
- **Better access control:** Allows you to manage which files and directories the application can access.
- **Security best practices:** Helps align your Docker images with common production security recommendations.

### Important Things to Remember

- Many Docker images run as root by default, but some images configure a non-root user already.
- The `USER` instruction changes the user for subsequent instructions and the container's runtime process.
- A non-root user must have the necessary permissions to access application files and directories.
- If an application needs to write logs, upload files, or create temporary files, ensure the user has permission to access the required locations.
- Running as a non-root user improves security, but it does not eliminate every security risk.

**DevOps tip:** Whenever possible, run application containers as a dedicated non-root user instead of using root unnecessarily.

---

## What Is LABEL?

The `LABEL` instruction adds metadata to a Docker image.

This metadata can describe the image's purpose, version, maintainer, project name, or other useful information.

Labels do not directly change how an application runs. Instead, they help people and automation tools identify, organize, and manage images.

**In simple words:** `LABEL` adds descriptive information to your Docker image.

## Example: Adding Labels to an Image

```dockerfile
FROM httpd:2.4

LABEL org.opencontainers.image.title="Apache Website"

LABEL org.opencontainers.image.version="1.0"

LABEL org.opencontainers.image.description="Dockerfile learning project"
```

### Understanding the Dockerfile

- `FROM httpd:2.4` — Uses Apache HTTP Server as the base image.
- `LABEL org.opencontainers.image.title="Apache Website"` — Specifies the title of the image.
- `LABEL org.opencontainers.image.version="1.0"` — Records the project's version.
- `LABEL org.opencontainers.image.description="Dockerfile learning project"` — Provides a short description of the image's purpose.

The labels use names from the Open Container Initiative (OCI) image specification, which defines commonly used metadata conventions.

You can also define multiple labels in a single instruction when appropriate.

## Build the Docker Image

Save the instructions in a file named `Dockerfile` and run:

```bash
docker build -t apache-site:v1 .
```

This creates an image named `apache-site` with the tag `v1`.

## Inspect the Image Metadata

To view the image's configuration and labels, run:

```bash
docker image inspect apache-site:v1
```

This command displays detailed information about the image, including its configuration, environment variables, and labels.

If you want to display only the labels, use:

```bash
docker image inspect --format '{{json .Config.Labels}}' apache-site:v1
```

You should see output containing the labels you defined, similar to:

```json
{
  "org.opencontainers.image.title": "Apache Website",
  "org.opencontainers.image.version": "1.0",
  "org.opencontainers.image.description": "Dockerfile learning project"
}
```

The output is formatted as JSON, making it easier to read the metadata.

## Why Do We Use LABEL?

Labels make it easier to identify and manage Docker images, especially when working with multiple applications and environments.

Some common uses include:

- **Image identification:** Describe what an image contains.
- **Version tracking:** Record the version associated with an image.
- **Project information:** Associate images with a project or team.
- **Automation:** Allow scripts and tools to inspect image metadata.
- **Image management:** Help teams organize images in larger environments.

### Important Things to Remember

- `LABEL` adds metadata to an image.
- Labels do not automatically configure or start the application.
- You can inspect labels using `docker image inspect`.
- Consistent label names make image metadata easier to manage.
- Do not store passwords, access tokens, or other secrets in labels because image metadata can be inspected.

---

## USER vs LABEL

Although both instructions are used in Dockerfiles, their purposes are completely different.

| Feature | USER | LABEL |
|---|---|---|
| Primary purpose | Selects the user for subsequent instructions and runtime | Adds metadata to the image |
| Changes runtime behavior? | Yes, by determining the configured runtime user | Not directly |
| Main use | Security and access control | Identification and organization |
| Example | `USER appuser` | `LABEL org.opencontainers.image.version="1.0"` |
| Can be inspected? | Yes, through image configuration and container processes | Yes, through image configuration |

## Practice Tasks

Test your understanding by completing these exercises:

1. Create a Docker image using Ubuntu 24.04.
2. Create a non-root user named `appuser`.
3. Use `USER` to run the container as `appuser`.
4. Run the container and use the `id` command to verify the current user.
5. Add image title, version, and description labels to an Apache image.
6. Build the image and inspect its labels using `docker image inspect`.
7. Explain why running an application as a non-root user is generally safer than running it as root.

## Key Takeaway

**`USER` controls which user runs subsequent instructions and the container's runtime process, while `LABEL` adds descriptive metadata to the image.**

For example, you can use `USER appuser` to run an application with fewer privileges and use `LABEL` to record the application's title and version.

Both instructions are useful when building Docker images that are easier to manage and better aligned with production DevOps practices.
