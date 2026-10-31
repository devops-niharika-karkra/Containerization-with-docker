# 07 — CMD and ENTRYPOINT Instructions

## What Is CMD?

The `CMD` instruction specifies the default command or arguments that Docker uses when a container starts.

It is commonly used to define what a container should do when you run it without providing a different command.

**In simple words:** `CMD` tells Docker what to do by default when a container starts.

### Example

```dockerfile
FROM ubuntu:24.04

CMD ["echo", "Hello from CMD"]
```

### Understanding the Dockerfile

- `FROM ubuntu:24.04` — Uses Ubuntu 24.04 as the base image.
- `CMD ["echo", "Hello from CMD"]` — Specifies the default command that displays a message when the container starts.

### Build and Run

**Step 1: Build the image**

```bash
docker build -t cmd-demo .
```

**Step 2: Run the container**

```bash
docker run --rm cmd-demo
```

**Expected output:**

```text
Hello from CMD
```

Docker executes the default command specified by `CMD` when the container starts.

### What Happens If You Override CMD?

You can provide a different command when running the container.

For example:

```bash
docker run --rm cmd-demo echo "Hello DevOps"
```

**Output:**

```text
Hello DevOps
```

The command supplied at runtime replaces the default `CMD` command.

This makes `CMD` useful when you want to provide a default behavior but still allow users to change it when needed.

---

## What Is ENTRYPOINT?

The `ENTRYPOINT` instruction specifies the main executable that runs when a container starts.

Unlike a standalone `CMD`, an `ENTRYPOINT` is not normally replaced by the command-line arguments supplied to `docker run`. Instead, those arguments are passed to the configured entrypoint.

**In simple words:** `ENTRYPOINT` defines the main program your container is designed to run.

### Example

```dockerfile
FROM ubuntu:24.04

ENTRYPOINT ["echo"]
CMD ["Hello from ENTRYPOINT"]
```

### Understanding the Dockerfile

- `FROM ubuntu:24.04` — Uses Ubuntu 24.04 as the base image.
- `ENTRYPOINT ["echo"]` — Sets `echo` as the main executable.
- `CMD ["Hello from ENTRYPOINT"]` — Provides the default argument to the entrypoint.

Here, `ENTRYPOINT` and `CMD` work together. The entrypoint specifies the executable, while `CMD` provides its default argument.

### Run with Default Arguments

```bash
docker build -t entrypoint-demo .
docker run --rm entrypoint-demo
```

**Expected output:**

```text
Hello from ENTRYPOINT
```

Docker effectively executes:

```bash
echo "Hello from ENTRYPOINT"
```

### Override the Default Arguments

Now, run the same image with a different message:

```bash
docker run --rm entrypoint-demo "Hello DevOps"
```

**Expected output:**

```text
Hello DevOps
```

Notice that `echo` still runs. Only the default argument provided by `CMD` is replaced.

This is one of the most useful features of combining `ENTRYPOINT` and `CMD`.

---

## CMD vs ENTRYPOINT

Both instructions help define what happens when a container starts, but they serve different purposes.

| Feature | CMD | ENTRYPOINT |
|---|---|---|
| Main purpose | Specifies a default command or arguments | Defines the main executable |
| Can runtime arguments replace it? | Yes, normally | No, not in the usual exec-form configuration |
| Can work together? | Yes | Yes |
| Common use | Providing default behavior | Defining the primary application or executable |

### Example 1: Using CMD Alone

```dockerfile
FROM ubuntu:24.04

CMD ["echo", "Hello Docker"]
```

Run:

```bash
docker run --rm cmd-demo
```

Output:

```text
Hello Docker
```

Now override the default command:

```bash
docker run --rm cmd-demo echo "Hello DevOps"
```

Output:

```text
Hello DevOps
```

The runtime command replaces the default `CMD`.

### Example 2: Combining ENTRYPOINT and CMD

```dockerfile
FROM ubuntu:24.04

ENTRYPOINT ["echo"]
CMD ["Hello Docker"]
```

Run:

```bash
docker run --rm entrypoint-demo
```

Output:

```text
Hello Docker
```

Now provide a different argument:

```bash
docker run --rm entrypoint-demo "Hello DevOps"
```

Output:

```text
Hello DevOps
```

In this case, `echo` remains the executable, while the runtime argument replaces the default argument supplied by `CMD`.

**Remember:**
- `CMD` provides the default behavior.
- `ENTRYPOINT` defines the main executable.
- When used together, `ENTRYPOINT` specifies the executable and `CMD` supplies its default arguments.

---

## Shell Form vs Exec Form

Docker supports two common ways to write `CMD` and `ENTRYPOINT`: shell form and exec form.

Understanding the difference helps you write Dockerfiles that behave more predictably.

### 1. Exec Form

Exec form uses a JSON array.

Example:

```dockerfile
CMD ["sleep", "30"]
```

Here:
- `sleep` is the executable.
- `30` is the argument passed to it.

Docker runs the executable directly rather than launching it through a shell.

**Why is this useful?**

Exec form generally provides more predictable signal handling because the application can run as the container's main process.

### 2. Shell Form

Shell form uses a command written as a regular string.

Example:

```dockerfile
CMD sleep 30
```

Docker normally runs this command through a shell, such as `/bin/sh -c` on Linux images that provide it.

This introduces an additional shell process, which can affect how signals are delivered to the application.

### Which Form Should You Use?

For most applications, **exec form is generally preferred**.

For example:

```dockerfile
CMD ["sleep", "30"]
```

This allows `sleep` to run directly as the main process, making signal handling more predictable.

Shell form can still be useful when you specifically need shell features such as variable expansion, pipes, or command chaining.

For example:

```dockerfile
CMD ["sh", "-c", "echo Hello && sleep 30"]
```

This uses exec form to launch a shell explicitly, allowing the shell to interpret `&&`.

---

## Why Is Signal Handling Important?

When you stop a running container, Docker sends a stop signal—normally `SIGTERM`—to the container's main process and eventually forces termination if the process does not stop within the configured grace period.

If your application handles signals correctly, it can shut down gracefully instead of stopping abruptly.

This is especially important for applications running in production environments.

For example, an application may need to:
- Finish processing ongoing requests.
- Close database connections.
- Save important state.
- Release resources before exiting.

Using exec form helps ensure that the intended application process receives stop signals directly, although the application must still handle those signals correctly.

## Practice Tasks

Test your understanding by completing the following exercises:

1. Create a Docker image using `CMD` to display a message.
2. Create another image using `ENTRYPOINT` to run `echo`.
3. Combine `ENTRYPOINT` and `CMD` to define an executable and its default argument.
4. Run the container with a different argument and observe what changes.
5. Experiment with the shell form and exec form of `CMD`.
6. Run a container with a long-running command, such as `sleep 300`, and stop it using `docker stop`. Observe how the process terminates.

## Key Takeaway

**`CMD` defines the default command or arguments, while `ENTRYPOINT` defines the main executable.**

When combined, `ENTRYPOINT` specifies what runs, and `CMD` provides the default arguments.

Understanding these instructions will help you build Docker images that are easier to run, configure, and manage in real-world DevOps projects.
