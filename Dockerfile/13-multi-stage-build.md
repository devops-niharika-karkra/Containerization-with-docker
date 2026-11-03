# 13 — Multi-Stage Builds

## 1. What Is a Multi-Stage Build?

A Multi-Stage Build is a Docker technique that allows us to use multiple `FROM` instructions in a single Dockerfile.

It helps us separate the **application building process** from the **application running process**.

Let's understand this with a simple example.

Imagine you are developing a Go application.

To build this application, you need:
- Go compiler
- Source code
- Development tools
- Required dependencies

But after compiling the application, you may only need the compiled executable to run it.

So, why should we include the compiler and all development tools in our final Docker image if they are no longer required?

This is where Multi-Stage Builds become useful.

We use one stage to build the application and another stage to create the final image containing only the files required to run it.

## 2. How Does a Multi-Stage Build Work?

A Multi-Stage Build generally has two important stages:

**Stage 1 — Build Stage**

- Contains the compiler and development tools.
- Copies the application source code.
- Compiles the application.
- Produces the final executable or build output.

**Stage 2 — Final Stage**

- Starts with a fresh base image.
- Copies only the required output from the build stage.
- Contains the files needed to run the application.

This helps keep unnecessary development tools out of the final image.

## 3. Example — Multi-Stage Build Using Go

Let's understand this with a practical Dockerfile.

```dockerfile
# Stage 1: Build the application
FROM golang:1.25 AS builder

# Set the working directory
WORKDIR /src

# Copy application source code
COPY . .

# Compile the Go application
RUN go build -o /app/server .

# Stage 2: Create the final image
FROM scratch

# Copy the compiled executable from the builder stage
COPY --from=builder /app/server /server

# Define the application startup command
ENTRYPOINT ["/server"]
```

### Understanding Each Instruction

| Instruction | Purpose |
|---|---|
| `FROM golang:1.25 AS builder` | Starts the build stage using a Go image and names it `builder` |
| `WORKDIR /src` | Sets the working directory |
| `COPY . .` | Copies the application source code into the build stage |
| `RUN go build` | Compiles the Go application |
| `FROM scratch` | Starts a new, empty final image |
| `COPY --from=builder` | Copies the compiled executable from the builder stage |
| `ENTRYPOINT` | Defines the application executable that starts when the container runs |

**Note:** This example assumes the Go application compiles successfully and produces a self-contained executable. The final `scratch` image may not work for applications that require shared libraries, certificates, configuration files, or other runtime dependencies.

## 4. Important Instructions Used in Multi-Stage Builds

Let's understand some important concepts.

### A. `AS builder`

```dockerfile
FROM golang:1.25 AS builder
```

The `AS` keyword gives a name to a build stage.

Here, we have named the first stage `builder`.

This name allows us to refer to that stage later in the Dockerfile.

### B. Multiple `FROM` Instructions

```dockerfile
FROM golang:1.25 AS builder

FROM scratch
```

Each `FROM` instruction starts a new build stage.

The first stage is used to compile the application, while the second stage creates the final image.

### C. `COPY --from`

```dockerfile
COPY --from=builder /app/server /server
```

This instruction copies a file from one build stage into another.

In this example:
- `--from=builder` identifies the source stage.
- `/app/server` is the executable created in the builder stage.
- `/server` is the destination path in the final image.

This means we can take the required output from the build stage without copying all its development tools and source files into the final image.

### D. `FROM scratch`

```dockerfile
FROM scratch
```

`scratch` is a special empty base image.

It provides no operating system utilities, shell, package manager, or other standard files.

We use it when an application can run with only the files we explicitly copy into the image.

## 5. Why Do We Use Multi-Stage Builds?

Multi-Stage Builds provide several advantages:

1. **Smaller final images:** Unnecessary compilers and development tools are left behind in the build stage.
2. **Better security:** Fewer unnecessary tools and packages in the runtime image can reduce the attack surface.
3. **Clean separation:** Development dependencies and runtime dependencies remain separate.
4. **Better production readiness:** Helps create lightweight images containing only the files needed by the application.
5. **Efficient deployment:** Smaller images can be quicker to transfer and distribute, depending on the environment.

## 6. Important Considerations

Multi-Stage Builds are useful, but we should understand a few things before using them.

- Some applications require shared libraries to run.
- Some applications need CA certificates to establish secure HTTPS connections.
- Configuration files and other runtime assets may need to be copied into the final stage.
- The `scratch` image is not suitable for every application.
- A smaller image does not automatically mean a completely secure image.

**Best Practice:** Choose the final base image according to your application's runtime requirements. Do not blindly use `scratch` for every application.

## 7. Practical Lab — Understanding Multi-Stage Builds

Try creating a simple Go application and building it using multiple stages.

### Step 1: Create a Project Directory

```bash
mkdir multi-stage-demo
cd multi-stage-demo
```

### Step 2: Create a Go Application

Create a file named `main.go`:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello from Multi-Stage Docker Build!")
}
```

### Step 3: Create a Go Module

```bash
go mod init example.com/multi-stage-demo
```

This creates a `go.mod` file for the application.

### Step 4: Create the Dockerfile

```dockerfile
# Stage 1: Build the application
FROM golang:1.25 AS builder

WORKDIR /src

COPY go.mod ./
COPY main.go ./

RUN CGO_ENABLED=0 go build -o /app/server .

# Stage 2: Final runtime image
FROM scratch

COPY --from=builder /app/server /server

ENTRYPOINT ["/server"]
```

Here, `CGO_ENABLED=0` disables CGO for this simple application so that Go produces a self-contained executable suitable for the `scratch` image.

### Step 5: Build the Docker Image

```bash
docker build -t multi-stage-demo:v1 .
```

### Step 6: Run the Container

```bash
docker run --rm multi-stage-demo:v1
```

**Expected Output:**

```text
Hello from Multi-Stage Docker Build!
```

### Step 7: Inspect the Image Size

```bash
docker image ls multi-stage-demo
```

Observe the final image size.

You can also compare it with a single-stage image that uses the Go image as its runtime base.

Remember that the final image size depends on the base images, application, and build configuration.

## 8. Practice Tasks

Try completing these tasks yourself:

1. Find a simple application that requires compilation.
2. Create a Dockerfile with a separate builder stage.
3. Compile the application inside the builder stage.
4. Create a second stage using a suitable runtime image.
5. Copy only the required application files into the final stage.
6. Build and run the final Docker image.
7. Compare the image size with a single-stage build.
8. Identify which files and tools are required during building but unnecessary during runtime.
9. Explain why `COPY --from` is useful in Multi-Stage Builds.

## 9. Key Takeaway

Multi-Stage Builds help us create cleaner and more efficient Docker images by separating the application build environment from the final runtime environment.

Instead of carrying compilers, source code, and development tools into production, we can copy only the required application output into the final image.

**Remember:** Build the application in one stage, and run it in another stage with only the required runtime files.

## 10. Quick Revision

| Concept | Meaning |
|---|---|
| Multi-Stage Build | Uses multiple `FROM` instructions in one Dockerfile |
| Builder Stage | Stage where the application is compiled or built |
| Final Stage | Stage that creates the image used to run the application |
| `AS builder` | Gives a name to a build stage |
| `COPY --from` | Copies files from another build stage |
| `FROM scratch` | Starts with an empty base image |
| Main Benefit | Creates a cleaner and potentially smaller final image |

**DevOps Interview Tip:** Be prepared to explain the difference between single-stage and multi-stage builds, why we use `COPY --from`, and how multi-stage builds help optimize Docker images for production.
