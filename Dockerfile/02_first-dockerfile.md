# 02 — Creating My First Dockerfile

## Objective

In this practical, we will create our first custom Docker image using Apache HTTP Server and use it to serve a simple HTML webpage inside a Docker container.

This exercise will help you understand how to package website files into an image and make the application accessible through a web browser.

## Understanding the Dockerfile Instructions

We will use three basic Dockerfile instructions:

- **`FROM`** — Specifies the base image we want to use. Here, we are using the official Apache HTTP Server image, `httpd:2.4`.
- **`COPY`** — Copies our custom HTML file into the directory where Apache serves website content.
- **`EXPOSE`** — Documents the port the application is expected to use inside the container. It does not publish the port to the host by itself.

## Step 1: Create the Dockerfile

Create a project directory and place two files inside it: `Dockerfile` and `index.html`.

Add the following instructions to your `Dockerfile`:

```dockerfile
FROM httpd:2.4

COPY index.html /usr/local/apache2/htdocs/index.html

EXPOSE 80
```

Next, create an `index.html` file containing a simple webpage. For example:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My First Docker Website</title>
</head>
<body>
    <h1>My First Dockerfile Is Working!</h1>
    <p>This webpage is running inside an Apache Docker container.</p>
</body>
</html>
```

## Step 2: Build the Docker Image

Once both files are ready, open a terminal in the project directory and run:

```bash
docker build -t my-first-web:v1 .
```

This command builds a Docker image using the instructions in the Dockerfile and tags it as `my-first-web:v1`.

To confirm that the image has been created, run:

```bash
docker images
```

## Step 3: Run the Container

Now, create and start a container using your newly built image:

```bash
docker run -d --name my-first-web -p 8080:80 my-first-web:v1
```

Here is what each option means:

- **`-d`** — Runs the container in the background.
- **`--name my-first-web`** — Assigns a readable name to the container.
- **`-p 8080:80`** — Maps port `8080` on your host machine to port `80` inside the container.
- **`my-first-web:v1`** — Specifies the image used to create the container.

Apache listens on port 80 inside the container, while you access the website through port 8080 on your host machine.

## Step 4: Verify the Application

After starting the container, check whether it is running:

```bash
docker ps
```

Test the webpage from your terminal:

```bash
curl http://localhost:8080
```

You can also check the container logs for startup messages or errors:

```bash
docker logs my-first-web
```

Finally, open the following address in your browser:

`http://localhost:8080`

You should see the HTML page you created. If it appears correctly, your first custom Docker image and Apache container are working as expected.

## Step 5: Clean Up

After completing the exercise, stop and remove the container:

```bash
docker stop my-first-web
docker rm my-first-web
```

These commands stop the running container and remove it. The Docker image remains available on your machine until you
