# 09 — EXPOSE Instruction

## What Is EXPOSE?

The `EXPOSE` instruction tells Docker which port an application is expected to listen on inside a container.

It helps document how the containerized application is intended to communicate with other services or accept incoming connections.

For example, an Apache web server typically listens on port `80` for HTTP traffic.

**In simple words:** `EXPOSE` tells others which port your application is expected to use inside the container.

## Example

```dockerfile
FROM httpd:2.4

EXPOSE 80
```

### Understanding the Dockerfile

- `FROM httpd:2.4` — Uses an Apache HTTP Server image as the base image.
- `EXPOSE 80` — Documents that the container's application is expected to listen on port `80`.

This instruction does not start Apache or configure it to listen on port `80`. The application itself must be configured and running on that port.

## Does EXPOSE Publish the Port?

**No! The `EXPOSE` instruction does not automatically publish a container's port to your host machine or the internet.**

It documents the intended port in the image configuration, but you must publish the port separately if you want to access the application through a host port.

Docker provides the `-p` option for this purpose.

### Example: Publish a Container Port

Run the following command:

```bash
docker run -d --name apache-demo -p 8080:80 httpd:2.4
```

Let's break down the command:

- `docker run` — Creates and starts a container.
- `-d` — Runs the container in the background.
- `--name apache-demo` — Assigns the name `apache-demo` to the container.
- `-p 8080:80` — Maps port `8080` on the host to port `80` inside the container.
- `httpd:2.4` — Specifies the image used to create the container.

### Understanding Port Mapping

In the command `-p 8080:80`:

- **Host port: 8080** — The port you use to access the application from your host machine.
- **Container port: 80** — The port inside the container where Apache is expected to listen.

When you access port `8080` on your host, Docker forwards the published traffic to port `80` inside the container.

## Test the Website

After starting the container, run:

```bash
curl http://localhost:8080
```

If Apache is running correctly and the port mapping is working, you should receive an HTML response.

You can also open the following address in your browser:

`http://localhost:8080`

You should see the default Apache welcome page.

**Note:** If the website does not load, check whether the container is running, Apache is listening on port `80`, and the port mapping is correct.

## How Does Port Mapping Work?

Let's understand the flow of a request.

```text
Browser or curl
       |
       v
Host port 8080
       |
       v
Docker port mapping
       |
       v
Container port 80
       |
       v
Apache HTTP Server
       |
       v
HTTP Response
```

### Step-by-Step Explanation

1. You open `http://localhost:8080` in your browser.
2. The request reaches port `8080` on your host machine.
3. Docker forwards the published traffic to port `80` inside the container.
4. Apache receives the request and processes it.
5. Apache returns an HTTP response to your browser.

This is how you can access an application running inside a container through a port on your host machine.

## EXPOSE vs -p

Both are related to container ports, but they serve different purposes.

| Feature | EXPOSE | `-p` |
|---|---|---|
| Purpose | Documents the intended container port | Publishes a container port through a host port |
| Where is it used? | Inside the Dockerfile | With `docker run` |
| Makes the port accessible from the host by itself? | No | Yes, when the mapping and application are working |
| Example | `EXPOSE 80` | `-p 8080:80` |
| Can specify a host port? | No | Yes |

### Example Using Both

**Dockerfile:**

```dockerfile
FROM httpd:2.4

EXPOSE 80
```

Build the image:

```bash
docker build -t apache-demo:v1 .
```

Run the container:

```bash
docker run -d --name apache-demo -p 8080:80 apache-demo:v1
```

Test the website:

```bash
curl http://localhost:8080
```

Here, `EXPOSE 80` documents the intended container port, while `-p 8080:80` publishes that port through host port `8080`.

**Remember:** The host port and container port do not have to be the same.

For example, you can map host port `8081` to container port `80`:

```bash
docker run -d --name apache-demo -p 8081:80 httpd:2.4
```

You can then access the website at `http://localhost:8081`.

## Important Things to Remember

- `EXPOSE` documents the port an application is expected to use inside the container.
- `EXPOSE` does not automatically publish the port.
- The `-p` option publishes a container port through a host port.
- The host port and container port can be different.
- Publishing a port does not guarantee that the application is running or responding correctly.
- Host firewalls, network security rules, and other network configurations can affect external access.
- Publishing a port without specifying a host IP generally makes it accessible through the host's network interfaces, subject to firewall and network rules.

## Practice Tasks

Test your understanding by completing these exercises:

1. Create a Dockerfile using `httpd:2.4` and add `EXPOSE 80`.
2. Build the image and run a container using host port `8081`.
3. Access
