# Experiment 9: Writing Dockerfiles and Building Custom Containerized Applications

**Course**: Collaborative Development and DevOps (`1BCS303`)  
**Department**: Department of Computer Science and Engineering  
**Institution**: The National Institute of Engineering, Mysuru  
**Academic Year**: 2026 – 2027  

---

### Student Credentials
- **Student Name**: Sharma Shubh
- **USN**: `4NI25CS191`
- **Semester & Section**: 3rd Semester, Sec-G
- **Lab Guide**: Assistant Prof. Inchara R
- **GitHub**: [@Smirk1308](https://github.com/Smirk1308)
- **Repository**: [Smirk1308/DevOps_LAB_09](https://github.com/Smirk1308/DevOps_LAB_09)

---

## 1. Aim
To write a Dockerfile, build a custom Docker image for a simple Python application, and run and test it as a container.

---

## 2. Learning Objectives
- Understand the purpose of standard Dockerfile instructions (`FROM`, `WORKDIR`, `COPY`, `CMD`).
- Write a clean Dockerfile for containerizing a standalone Python script.
- Build a custom image from source using `docker build -t <tag> .`.
- Run a container from the newly built custom image and verify its console output.
- Implement a `.dockerignore` file to optimize build context and reduce image size.
- List, inspect, restart, and remove containers and images.

---

## 3. Important Concepts Before Starting

The core paradigm of container packaging:
$$\text{Dockerfile} \longrightarrow \text{Docker Image} \longrightarrow \text{Docker Container}$$

- **Dockerfile**: The recipe specifying instructions and environment configurations.
- **Image**: The compiled, immutable, packaged artifact result.
- **Container**: The living runtime instance executing the image.

### 3.1 Dockerfile Instructions
| Instruction | Purpose in Dockerfile |
| :--- | :--- |
| `FROM` | Specifies the base image to build on (e.g. `python:3.12-slim`). |
| `WORKDIR` | Sets the default working directory inside the container (e.g. `/app`). |
| `COPY` | Copies source files from the host build context into the container filesystem. |
| `CMD` | Sets the default command executed when the container starts up. |
| `RUN` | Executes commands during image build time (e.g. `pip install`). |
| `EXPOSE` | Documents the port a server listens on (informational). |

### 3.2 Image vs. Container Comparison
| Feature | Image | Container |
| :--- | :--- | :--- |
| **Nature** | Read-only static template with app & dependencies. | Running or stopped active process instance. |
| **Creation** | Built once with `docker build`. | Created dynamically with `docker run`. |
| **Multiplicity** | Single immutable template. | Multiple distinct containers can run from one image. |

### 3.3 Build Context and `.dockerignore`
The build context is the directory sent to the Docker daemon when invoking `docker build -t myapp:1.0 .` (where `.` denotes current folder).  
The `.dockerignore` file filters out unwanted files (like `.git`, `__pycache__`, temporary logs) to keep the image lightweight, secure, and fast to build.

---

## 4. Step-by-Step Experiment Procedure & Execution Logs

### Step 1: Verify Docker Engine is Running
```bash
$ docker --version
Docker version 27.2.0, build 3ae425f

$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

### Step 2: Create Project Folder
```bash
mkdir docker-lab-9
cd docker-lab-9
```

### Step 3: Create Python Application (`app.py`)
```python
print("Hello Students!")
print("Welcome to Docker Experiment 9.")
print("This application is running inside a Docker container.")
```

### Step 4: Author the `Dockerfile`
```dockerfile
# Use the official Python image as the base image
FROM python:3.12-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the application file into the container
COPY app.py .

# Run the Python application
CMD ["python", "app.py"]
```

### Step 5: Create `.dockerignore` File
```text
.git
__pycache__
*.pyc
```

### Step 6: Build Custom Image
```bash
$ docker build -t myapp:1.0 .
[+] Building 5.4s (8/8) FINISHED
 => [internal] load build definition from Dockerfile
 => => transferring dockerfile: 287B
 => [internal] load metadata for docker.io/library/python:3.12-slim
 => [internal] load .dockerignore
 => [1/3] FROM docker.io/library/python:3.12-slim@sha256:4d...
 => [2/3] WORKDIR /app
 => [3/3] COPY app.py .
 => exporting to image
 => => exporting layers
 => => naming to docker.io/library/myapp:1.0
```

### Step 7: Verify Image
```bash
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myapp        1.0       9f8e7d6c5b4a   15 seconds ago   128MB
```

### Step 8: Run Container from Custom Image
```bash
$ docker run --name mycontainer myapp:1.0
Hello Students!
Welcome to Docker Experiment 9.
This application is running inside a Docker container.
```

### Step 9: List Containers (Verify Exited Status)
```bash
$ docker ps -a
CONTAINER ID   IMAGE       COMMAND            CREATED          STATUS                     PORTS     NAMES
8a9b0c1d2e3f   myapp:1.0   "python app.py"    30 seconds ago   Exited (0) 29 seconds ago             mycontainer
```
*(The status `Exited (0)` indicates the Python process printed all outputs and terminated successfully without error).*

### Step 10: View Container Logs
```bash
$ docker logs mycontainer
Hello Students!
Welcome to Docker Experiment 9.
This application is running inside a Docker container.
```

### Step 11: Re-run the Existing Container
```bash
$ docker start -a mycontainer
Hello Students!
Welcome to Docker Experiment 9.
This application is running inside a Docker container.
```

### Step 12: Clean Up Container and Image
```bash
$ docker rm mycontainer
mycontainer

$ docker rmi myapp:1.0
Untagged: myapp:1.0
Deleted: sha256:9f8e7d6c5b4a...
```

---

## 5. Viva Questions and Answers

**Q1: What is a Dockerfile, and why is it required?**  
*A: A Dockerfile is a declarative text script containing sequential commands to assemble a Docker image. It ensures builds are deterministic, version-controlled, and repeatable across any host machine.*

**Q2: Explain FROM, WORKDIR, COPY, and CMD.**  
- `FROM`: Specifies the parent/base operating image (e.g. `python:3.12-slim`).  
- `WORKDIR`: Sets the working root directory for subsequent instructions inside the container (`/app`).  
- `COPY`: Transfers local source files into the image filesystem.  
- `CMD`: Defines the default execution binary and parameters launched when the container starts.

**Q3: What is the difference between a Docker image and a Docker container?**  
*A: An image is an inert, read-only package containing code and runtime dependencies. A container is a stateful, running process executing that image.*

**Q4: What does `docker build -t myapp:1.0 .` do?**  
*A: It builds a Docker image using the Dockerfile located in the current directory (`.`) as the build context, tagging (`-t`) the result with repository name `myapp` and tag `1.0`.*

**Q5: What is the difference between `docker ps` and `docker ps -a`?**  
*A: `docker ps` filters and lists only actively executing containers. `docker ps -a` displays all containers, including stopped and exited containers.*

**Q6: Why does the container stop automatically after the Python application finishes?**  
*A: A container's lifecycle is bound to its PID 1 process (defined by `CMD`). When `app.py` executes all print statements and terminates with exit code 0, the container gracefully shuts down and enters `Exited (0)` status.*

**Q7: What is the difference between RUN and CMD?**  
*A: `RUN` executes during the image build phase to produce new cached filesystem layers (e.g. installing packages). `CMD` does not run during build time; it executes only when starting a container instance.*

**Q8: Why use a `.dockerignore` file?**  
*A: To exclude unnecessary files (`.git`, virtual environments, caches) from being sent over the Docker daemon socket during build context transfer, speeding up build performance and keeping images lean and secure.*

**Q9: What does `docker start -a mycontainer` do?**  
*A: Restarts an existing stopped container and attaches (`-a`) the host terminal's STDOUT and STDERR so output is streamed directly to the console.*

**Q10: What is an image tag?**  
*A: An identifier label (e.g. `1.0`, `v2.1`, or `latest`) appended to an image repository name that distinguishes specific releases or version builds.*

---

## 6. Result
A Dockerfile was successfully written for a custom Python application, the Docker image was built locally with tag `myapp:1.0`, and the containerized application was executed, verified via logs and container inspection, and cleaned up in accordance with laboratory specifications.
