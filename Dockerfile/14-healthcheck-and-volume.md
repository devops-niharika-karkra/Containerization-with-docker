# 14 — HEALTHCHECK and VOLUME

## 1. HEALTHCHECK Instruction

### What Is HEALTHCHECK?

`HEALTHCHECK` is a Dockerfile instruction used to check whether an application running inside a container is working properly.

Think of it like a health monitor for your application.

A container can be running, but that does not always mean the application inside it is working correctly.

For example:
- A web server container may be running, but the website may not be responding.
- An application may have started successfully but may stop responding later.
- A service may be running but unable to handle requests.

This is where `HEALTHCHECK` becomes useful.

It allows Docker to periodically execute a command to check the application's health.

### Example

Suppose we have a web server running inside a container.

We can use the following instruction:

```dockerfile
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD curl -fsS http://127.0.0.1/ || exit 1
```

### Understanding Each Option

| Option | Purpose |
|---|---|
| `HEALTHCHECK` | Defines a health-check command |
| `--interval=30s` | Runs the health check every 30 seconds |
| `--timeout=5s` | Gives the command 5 seconds to complete |
| `--retries=3` | Marks the container as unhealthy after 3 consecutive failures |
| `CMD` | Specifies the command Docker executes |
| `curl -fsS` | Sends a request and returns a failure status for an unsuccessful HTTP response |
| `exit 1` | Indicates that the health check has failed |

**Important:** This example assumes that:
- A web server is running inside the container.
- The application responds at `http://127.0.0.1/`.
- `curl` is installed in the container.

### Understanding Container Health Status

Docker can report three health states:

1. **Starting:** Docker is still waiting for successful health-check results.
2. **Healthy:** The health-check command is succeeding.
3. **Unhealthy:** The configured number of consecutive checks has failed.

### How to Check Container Health?

You can use the following commands.

**1. List running containers:**

```bash
docker ps
```

If a health check is configured, you may see a status such as:

```text
Up 2 minutes (healthy)
```

**2. Inspect detailed health information:**

```bash
docker inspect --format='{{json .State.Health}}' container-name
```

This command displays health information, including the current status and recent health-check results.

### Important Note

`HEALTHCHECK` only monitors the health of an application. It does not automatically repair the application or restart an unhealthy container.

You can use the health status with orchestration and monitoring tools to take further action.

---

## 2. VOLUME Instruction

### What Is VOLUME?

`VOLUME` is a Dockerfile instruction used to declare a directory inside a container as a mount point for persistent or externally managed data.

Let's understand why we need it.

Imagine you are running a database inside a Docker container.

The database stores important information, such as:
- User details
- Password information
- Application records
- Transaction history

Now, suppose you remove the container and create a new one.

If your data was stored only in the container's writable layer, it may be lost when the container is removed.

To avoid this problem, Docker provides storage mechanisms such as volumes.

**Think of a Docker volume like an external storage drive.** Even if you remove the container, the data stored in the volume can remain available for another container.

### Example

```dockerfile
FROM ubuntu:24.04

VOLUME ["/data"]
```

### Understanding the Instructions

| Instruction | Purpose |
|---|---|
| `FROM ubuntu:24.04` | Selects Ubuntu as the base image |
| `VOLUME ["/data"]` | Declares `/data` as a volume mount point |

When a container is created from this image without an explicit mount, Docker creates an anonymous volume for this mount point.

You can also attach a named volume when running the container.

---

## 3. Practical Lab — Understanding Docker Volumes

Let's create a named volume and check whether our data remains available even after removing the container.

### Step 1: Create a Named Volume

```bash
docker volume create app-data
```

This command creates a Docker volume named `app-data`.

### Step 2: Run a Container With the Volume

```bash
docker run -d \
  --name volume-demo \
  -v app-data:/data \
  ubuntu:24.04 sleep infinity
```

### Understanding the Command

| Option | Purpose |
|---|---|
| `docker run -d` | Runs the container in the background |
| `--name volume-demo` | Assigns a name to the container |
| `-v app-data:/data` | Mounts the named volume at `/data` |
| `ubuntu:24.04` | Uses Ubuntu as the container image |
| `sleep infinity` | Keeps the container running |

### Step 3: Create a File Inside the Volume

Run the following command:

```bash
docker exec volume-demo sh -c 'echo "Docker Volume Test" > /data/test.txt'
```

This command creates a file named `test.txt` inside `/data`.

Because `/data` is connected to the named volume, the file is stored in the volume rather than only in the container's writable layer.

### Step 4: Verify the File

```bash
docker exec volume-demo cat /data/test.txt
```

**Expected Output:**

```text
Docker Volume Test
```

### Step 5: Remove the Container

```bash
docker rm -f volume-demo
```

This removes the container but does not remove the named volume.

### Step 6: Create Another Container Using the Same Volume

```bash
docker run -d \
  --name volume-demo-new \
  -v app-data:/data \
  ubuntu:24.04 sleep infinity
```

We have created a new container and attached the same named volume.

### Step 7: Check Whether the Data Still Exists

```bash
docker exec volume-demo-new cat /data/test.txt
```

**Expected Output:**

```text
Docker Volume Test
```

Great! The file is still available even though the original container was removed.

This demonstrates how Docker volumes help preserve data independently of a container's lifecycle.

### Step 8: Inspect the Volume

```bash
docker volume inspect app-data
```

This command displays information about the volume, including its name, driver, and mount location.

---

## 4. Why Is Persistent Storage Important?

Containers are designed to be replaceable.

For example, in a DevOps environment, we may:
- Remove an old container.
- Deploy a new container version.
- Recreate containers during application updates.
- Scale applications by creating additional containers.

If important data is stored only inside a container's writable layer, removing that container can result in data loss.

Docker volumes help solve this problem by storing data independently of the container.

### Common Use Cases

| Use Case | Why Volumes Are Used |
|---|---|
| Databases | Store database files |
| Application data | Preserve important application information |
| Logs | Store logs outside the container when appropriate |
| Configuration | Share externally managed configuration files |
| Backups | Provide a storage location for backup data |

**Important:** Volumes help preserve data, but they are not a replacement for backups. You should always have a proper backup and recovery strategy.

---

## 5. Best Practices

### For HEALTHCHECK

1. Use a health-check command that actually verifies application functionality.
2. Make sure the required command-line tools are installed inside the container.
3. Choose suitable intervals and timeouts.
4. Avoid overly frequent checks that create unnecessary load.
5. Remember that an unhealthy status does not automatically restart or repair the application.

### For VOLUME

1. Use named volumes when you need predictable storage management.
2. Store important application data outside the container's writable layer.
3. Avoid deleting volumes accidentally.
4. Maintain regular backups of important data.
5. Test your backup and recovery process.

---

## 6. Practice Tasks

Try completing these tasks yourself:

**HEALTHCHECK Tasks**

1. Create a Dockerfile using an appropriate web server image.
2. Install or use an available health-check tool such as `curl`.
3. Add a `HEALTHCHECK` instruction.
4. Build and run the container.
5. Check the container's health using `docker ps`.
6. Inspect detailed health information using `docker inspect`.
7. Explain the difference between a running container and a healthy container.

**VOLUME Tasks**

1. Create a named volume called `my-volume`.
2. Run an Ubuntu container and mount the volume at `/data`.
3. Create a test file inside `/data`.
4. Remove the container.
5. Create another container using the same volume.
6. Verify that the test file still exists.
7. Inspect the volume using `docker volume inspect`.
8. Explain why volumes still need a backup and recovery strategy.

## 7. Key Takeaway

Both `HEALTHCHECK` and `VOLUME` help us manage containers more effectively, but they solve different problems.

- **HEALTHCHECK:** Monitors whether an application inside a container is working properly.
- **VOLUME:** Provides a way to store data independently of a container's lifecycle.

Remember:

A running container does not always mean a healthy application, and a removed container does not necessarily mean its volume data is lost.

## 8. Quick Revision

| Concept | Meaning |
|---|---|
| `HEALTHCHECK` | Checks application health inside a container |
| `--interval` | Time between health checks |
| `--timeout` | Maximum time allowed for a check |
| `--retries` | Consecutive failures needed to mark the container unhealthy |
| `docker inspect` | Displays detailed container information |
| `VOLUME` | Declares a mount point for persistent or externally managed data |
| Named Volume | A volume managed by Docker using a specific name |
| `docker volume create` | Creates a named volume |
| `docker volume inspect` | Displays volume information |
| `-v app-data:/data` | Mounts a named volume inside a container |

**DevOps Interview Tip:** Be prepared to explain why a container can be running but unhealthy, the difference between a Docker volume and a container's writable layer, and how named volumes help preserve data when containers are recreated.
