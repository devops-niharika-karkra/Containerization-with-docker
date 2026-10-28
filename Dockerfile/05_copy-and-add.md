# 05 — COPY and ADD Instructions

## What Is COPY?

The `COPY` instruction is used to copy files and directories from your local build context into a Docker image.

For example, if you have an HTML file on your system and want to include it in an Apache web server image, you can use `COPY` to place that file in the appropriate directory.

**In simple words:** `COPY` takes files from your project and places them inside the Docker image.

## Example: Copy an HTML File

```dockerfile
FROM httpd:2.4

COPY index.html /usr/local/apache2/htdocs/index.html
```

### Understanding the Example

- `FROM httpd:2.4` — Uses an Apache HTTP Server image as the base image.
- `COPY index.html /usr/local/apache2/htdocs/index.html` — Copies your local `index.html` file into the directory where Apache serves website files.

After building the image and running a container, Apache can serve your HTML page to visitors.

**Note:** The `index.html` file must be available in the build context and must not be excluded by your `.dockerignore` file.

## What Is ADD?

The `ADD` instruction also copies files and directories into a Docker image. However, it provides some additional capabilities that `COPY` does not.

One important feature is that `ADD` can automatically extract supported local tar archives into the destination directory.

**In simple words:** `ADD` can copy files like `COPY`, but it can also handle certain additional operations, such as extracting local archives.

## Example: Extract a Tar Archive

```dockerfile
FROM ubuntu:24.04

ADD website.tar.gz /opt/website/
```

### Understanding the Example

- `FROM ubuntu:24.04` — Uses Ubuntu 24.04 as the base image.
- `ADD website.tar.gz /opt/website/` — Copies the local archive into the image and automatically extracts its contents into `/opt/website/`, provided it is a supported tar archive.

For example, if the archive contains HTML, CSS, and image files, those files will be extracted into the destination directory.

**Important:** The archive must be available in the build context. Docker's automatic extraction applies to supported local tar archives, not every compressed file format.

## Practical Example: Hosting a Website Using Apache

Let's combine what we have learned and create a Docker image that contains an HTML file and an assets directory.

### Step 1: Create the Project Structure

Arrange your files like this:

```text
website/
├── Dockerfile
├── index.html
└── assets/
    └── style.css
```

Here:
- `Dockerfile` contains the instructions to build the image.
- `index.html` contains the main webpage.
- `assets/` contains supporting files, such as CSS stylesheets.

### Step 2: Write the Dockerfile

```dockerfile
FROM httpd:2.4

COPY index.html /usr/local/apache2/htdocs/index.html

COPY assets/ /usr/local/apache2/htdocs/assets/

EXPOSE 80
```

### Step 3: Understand the Instructions

- `FROM httpd:2.4` — Uses Apache as the base image.
- `COPY index.html ...` — Copies the main HTML file into Apache's default document directory.
- `COPY assets/ ...` — Copies the contents of the local `assets` directory into the image.
- `EXPOSE 80` — Documents that the container's application is intended to listen on port 80. It does not publish the port by itself.

### Step 4: Build the Image

Open a terminal in the `website` directory and run:

```bash
docker build -t my-website:v1 .
```

This builds the Docker image using the files in the current directory as the build context.

### Step 5: Run the Container

```bash
docker run -d --name website-container -p 8080:80 my-website:v1
```

This command:
- `-d` — Runs the container in the background.
- `--name website-container` — Assigns a name to the container.
- `-p 8080:80` — Maps port 8080 on your machine to port 80 inside the container.
- `my-website:v1` — Specifies the image to run.

Now open your browser and visit:

`http://localhost:8080`

You should see your HTML page served by Apache.

## COPY vs ADD

Both instructions can copy files into an image, but they have different capabilities.

| Feature | COPY | ADD |
|---|---|---|
| Copy local files | Yes | Yes |
| Copy directories | Yes | Yes |
| Automatically extract supported local tar archives | No | Yes |
| Support additional source types in Docker builds | No | Yes |
| Recommended for ordinary file copying | Yes | Usually not needed |

### Which One Should You Use?

For most everyday Dockerfile tasks, **prefer `COPY`** because its purpose is clear: it copies files and directories into the image.

Use `ADD` when you specifically need one of its additional capabilities, such as automatic extraction of a supported local tar archive.

Using `COPY` for ordinary file transfers makes your Dockerfile easier to understand and maintain.

## Important Concept: Build Context

The **build context** is the set of files and directories Docker can access when building an image.

For example, when you run:

```bash
docker build -t my-website:v1 .
```

The `.` specifies the current directory as the build context.

Docker can use files from this context in `COPY` and `ADD` instructions, provided those files are not excluded by `.dockerignore`.

### Why Does the Build Context Matter?

Suppose your Dockerfile contains:

```dockerfile
COPY index.html /usr/local/apache2/htdocs/
```

If `index.html` is not available in the build context, Docker will not be able to copy it, and the build will fail.

To avoid this problem:
- Keep the required files inside the build context.
- Check that the file paths in your Dockerfile are correct.
- Review `.dockerignore` if Docker cannot find a file that exists on your system.
- Avoid sending unnecessary files into the build context.

## Practice Tasks

Test your understanding by completing these tasks:

1. Create a Docker image using `httpd:2.4` and copy an HTML file into it.
2. Create an `assets` directory containing a CSS file and copy it into the Apache document directory.
3. Build and run the image, then verify that your website is accessible through a browser.
4. Create a local tar archive and use `ADD` to extract it into an Ubuntu image.
5. Compare `COPY` and `ADD` and explain why `COPY` is generally preferred for simple file operations.
6. Experiment with `.dockerignore` and observe what happens when you try to copy an excluded file.

## Key Takeaway

**`COPY` and `ADD` help you include files and directories in your Docker image.**

- Use `COPY` for normal file and directory transfers.
- Use `ADD` when you need its additional features, such as automatic extraction of supported local tar archives.
- Understand the build context so Docker can access the files your Dockerfile needs.

These instructions are especially useful when packaging website files, application code, configuration files, and other resources into Docker images.
