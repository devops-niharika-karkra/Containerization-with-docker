# 11 — .dockerignore

## 1. What Is `.dockerignore`?

`.dockerignore` is a special file used in Docker to tell Docker which files and directories should be excluded from the **build context**.

The build context is the collection of files and directories that Docker can access when building an image.

For example, when you run:

```bash
docker build -t my-image .
```

The `.` means that the current directory is used as the build context.

Docker sends the required build-context files to the builder. A `.dockerignore` file helps exclude files that are not needed.

Think of it like packing a bag: you only put the things you need and leave unnecessary items behind.

## 2. Example of a `.dockerignore` File

Create a file named `.dockerignore` in the same directory as your Dockerfile.

Add the following patterns:

```dockerignore
.git
.gitignore
.env
*.log
node_modules/
tmp/
```

### Understanding Each Pattern

| Pattern | What It Excludes |
|---|---|
| `.git` | Git repository directory |
| `.gitignore` | Git ignore configuration file |
| `.env` | Local environment file |
| `*.log` | All files ending with `.log` |
| `node_modules/` | Node.js dependency directory |
| `tmp/` | Temporary files directory |

**Important:** These are examples of patterns. You can add or remove patterns depending on your project requirements.

## 3. Why Do We Use `.dockerignore`?

There are several reasons to use this file:

1. **Reduce build-context size:** Excludes unnecessary files before they are sent to the builder.
2. **Improve build performance:** A smaller build context can make builds faster, especially when working with large projects or remote builders.
3. **Avoid unnecessary files:** Keeps irrelevant files, such as logs and temporary files, out of the build context.
4. **Reduce accidental exposure:** Helps prevent local files, such as `.env` files, from being included in the build context.

## 4. Example Project Directory

Suppose your project has the following structure:

```text
my-project/
│
├── Dockerfile
├── .dockerignore
├── index.html
├── .git/
├── .env
└── debug.log
```

Now, assume your `.dockerignore` contains:

```dockerignore
.git
.env
*.log
```

Docker will exclude the following:

- `.git/` directory
- `.env` file
- `debug.log` file

The remaining files, such as `Dockerfile` and `index.html`, remain available in the build context.

## 5. Practical Lab — Understanding `.dockerignore`

Let's perform a small practical exercise.

### Step 1: Create a Project Directory

```bash
mkdir dockerignore-demo
cd dockerignore-demo
```

### Step 2: Create the Required Files

```bash
touch Dockerfile index.html .env debug.log
mkdir .git
```

### Step 3: Add Content to the Dockerfile

```dockerfile
FROM ubuntu:24.04

WORKDIR /app

COPY . .

CMD ["ls", "-la"]
```

Here:

- `FROM` selects the base image.
- `WORKDIR` sets the working directory inside the container.
- `COPY . .` copies the available build-context files into `/app`.
- `CMD` displays the files when the container starts.

### Step 4: Create the `.dockerignore` File

```bash
cat > .dockerignore <<EOF
.git
.env
*.log
EOF
```

### Step 5: Build the Image

```bash
docker build -t dockerignore-demo:v1 .
```

### Step 6: Run the Container

```bash
docker run --rm dockerignore-demo:v1
```

You should see the files copied into `/app`, but the excluded `.git`, `.env`, and `debug.log` files should not appear.

## 6. Important Practical Concept

What happens if you try to copy an excluded file?

For example, add this instruction to your Dockerfile:

```dockerfile
COPY .env /app/.env
```

Then build the image again:

```bash
docker build -t dockerignore-demo:v2 .
```

**Expected result:** The build will fail because `.env` was excluded from the build context and is therefore unavailable to `COPY`.

This is an important concept to understand: Docker cannot copy a file from the build context if that file has been excluded by `.dockerignore`.

## 7. Important Security Note

Never depend on `.dockerignore` as your only method of protecting sensitive information.

For example, files such as these should generally be excluded from the build context:

- `.env`
- Private keys
- Password files
- Local credentials
- Secret configuration files

However, remember that `.dockerignore` does not protect secrets that are already included through other methods.

If an application needs credentials during a build, use Docker BuildKit secret mounts. For runtime credentials, use appropriate secret-management mechanisms.

**Best Practice:** Never hardcode passwords, API keys, or other sensitive information inside a Dockerfile or copy them into image layers.

## 8. Practice Tasks

Try completing these tasks yourself:

1. Create a project directory containing a Dockerfile and some sample files.
2. Create a `.dockerignore` file.
3. Exclude the `.git` directory.
4. Exclude all `.log` files using a wildcard pattern.
5. Exclude the `.env` file.
6. Build the Docker image and check which files are copied into the container.
7. Try using `COPY` to copy an excluded file and observe the build error.
8. Explain why excluding local environment files is a good practice.

## 9. Key Takeaway

`.dockerignore` helps control which files and directories are included in the Docker build context.

It reduces unnecessary data, can improve build efficiency, and helps prevent accidental inclusion of local files.

**Remember:** Dockerfile tells Docker how to build an image, while `.dockerignore` tells Docker which build-context files to leave out.

## 10. Quick Revision

| Concept | Meaning |
|---|---|
| `.dockerignore` | Excludes matching files and directories from the build context |
| Build Context | Files and directories available to the Docker builder |
| `*.log` | Excludes files ending with `.log` |
| `.env` | Commonly excluded local environment file |
| `node_modules/` | Excludes Node.js dependencies |
| `COPY` | Cannot copy files excluded from the build context |

**DevOps Interview Tip:** Be ready to explain the difference between `.gitignore` and `.dockerignore`.

- `.gitignore` tells Git which files to ignore.
- `.dockerignore` tells Docker which files to exclude from the build context.
