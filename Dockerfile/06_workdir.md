# 06 — WORKDIR Instruction

## What Is WORKDIR?

The `WORKDIR` instruction sets the working directory for the instructions that follow it in a Dockerfile.

Instead of repeatedly specifying the full path whenever you want to work with a file or directory, you can set a working directory once and use relative paths afterward.

If the specified directory does not exist, Docker creates it automatically.

**In simple words:** `WORKDIR` tells Docker which directory to work in.

## Syntax

```dockerfile
WORKDIR /path/to/directory
```

Here, `/path/to/directory` is the location where you want Docker to perform subsequent operations.

## Example

Let's create a simple Docker image using Ubuntu and set `/app` as the working directory.

```dockerfile
FROM ubuntu:24.04

WORKDIR /app

COPY app.txt .

CMD ["pwd"]
```

### Understanding the Dockerfile

- `FROM ubuntu:24.04` — Uses Ubuntu 24.04 as the base image.
- `WORKDIR /app` — Sets `/app` as the working directory. If the directory does not exist, Docker creates it.
- `COPY app.txt .` — Copies the local `app.txt` file into the current working directory inside the image. In this example, the destination is `/app/app.txt`.
- `CMD ["pwd"]` — Displays the current working directory when the container starts.

**Important:** The `app.txt` file must be available in the build context and must not be excluded by `.dockerignore`.

## Practical Lab

Let's build and run the image to see how `WORKDIR` behaves.

### Step 1: Create the Required Files

Create a directory for your project and add these two files:

```text
workdir-project/
├── Dockerfile
└── app.txt
```

Add some sample text to `app.txt`.

### Step 2: Build the Docker Image

Open your terminal inside the `workdir-project` directory and run:

```bash
docker build -t workdir-demo:v1 .
```

This command builds the image using the instructions in your Dockerfile.

During the build, Docker creates `/app` if necessary and copies `app.txt` into that directory.

### Step 3: Run the Container

```bash
docker run --rm workdir-demo:v1
```

### Expected Output

```text
/app
```

The output confirms that `/app` is the working directory when the container starts.

You can also verify that the file was copied correctly by running:

```bash
docker run --rm --entrypoint ls workdir-demo:v1 -l /app
```

You should see `app.txt` listed in the output.

## Why Do We Use WORKDIR?

The `WORKDIR` instruction makes Dockerfiles easier to write, read, and maintain.

Some important benefits include:

- **Better organization:** Keep application files and related operations organized in a specific directory.
- **Simpler file paths:** Use relative paths instead of repeatedly writing long absolute paths.
- **Improved readability:** Make it easier to understand where commands will execute.
- **Fewer path-related mistakes:** Establish a predictable working directory for subsequent instructions.
- **Consistent execution:** Set the default working directory for the container's runtime command when the final `WORKDIR` remains in effect.

## Important Things to Remember

- `WORKDIR` creates the specified directory if it does not already exist.
- It affects subsequent instructions in the Dockerfile, including instructions such as `RUN`, `COPY`, and `CMD` where their behavior depends on the working directory.
- Relative paths are interpreted in relation to the current working directory.
- An absolute path, such as `/var/log/app`, specifies a location independently of the current working directory.
- Setting `WORKDIR` does not move existing files from other directories in the base image.
- You can use multiple `WORKDIR` instructions in a Dockerfile. A relative path in a later instruction is resolved relative to the current working directory.

### Example: Using Multiple WORKDIR Instructions

```dockerfile
FROM ubuntu:24.04

WORKDIR /app

WORKDIR logs

RUN pwd
```

**Expected output during the build:**

```text
/app/logs
```

Why?

- The first `WORKDIR` sets the working directory to `/app`.
- The second `WORKDIR` uses the relative path `logs`, making the new working directory `/app/logs`.
- The `RUN pwd` command displays the resulting path.

If you had used `WORKDIR /logs` instead, the working directory would have been `/logs`, because the path starts with `/` and is therefore absolute.

## Practice Tasks

Try these exercises to strengthen your understanding:

1. Set the working directory to `/app` in a Dockerfile.
2. Copy a local file into `/app` using a relative destination.
3. Create another directory named `/app/logs` using an absolute path.
4. Use the `pwd` command to check the working directory.
5. Use `ls` to verify the location of your copied files.
6. Add a second `WORKDIR` instruction using a relative path and observe how the working directory changes.

## Key Takeaway

**The `WORKDIR` instruction establishes a predictable working location for subsequent Dockerfile instructions and the container's default command.**

For example, if your application files are stored in `/app`, setting `WORKDIR /app` allows you to work with those files without repeatedly specifying the full directory path.

This keeps your Dockerfile cleaner, more readable, and easier to maintain.
