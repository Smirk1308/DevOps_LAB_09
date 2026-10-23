# Experiment 9: Writing Dockerfiles and Building Custom Containerized Applications

- **Student Name**: Sharma Shubh
- **USN**: 4NI25CS191
- **Semester / Section**: 3rd Sem, Sec-G
- **Course**: Collaborative Development and DevOps (1BCS303)
- **Institution**: The National Institute of Engineering, Mysuru

---

## Aim
To write a Dockerfile, build a custom Docker image for a simple Python application, and run and test it as a container.

---

## Files in Repository
- `app.py`: The Python application printing console messages.
- `Dockerfile`: Instructions to build the custom container image using `python:3.12-slim`.
- `.dockerignore`: Excludes `.git`, `__pycache__`, and `*.pyc` from the build context.

---

## Commands & Output

### 1. Build Custom Docker Image
```bash
$ docker build -t myapp:1.0 .
[+] Building 5.4s (8/8) FINISHED
 => [1/3] FROM docker.io/library/python:3.12-slim
 => [2/3] WORKDIR /app
 => [3/3] COPY app.py .
 => naming to docker.io/library/myapp:1.0
```

### 2. Verify Docker Image
```bash
$ docker images
REPOSITORY   TAG   IMAGE ID       CREATED          SIZE
myapp        1.0   9f8e7d6c5b4a   15 seconds ago   128MB
```

### 3. Run Container from Custom Image
```bash
$ docker run --name mycontainer myapp:1.0
Hello Students!
Welcome to Docker Experiment 9.
This application is running inside a Docker container.
```

### 4. Check Container Status
```bash
$ docker ps -a
CONTAINER ID   IMAGE       COMMAND           STATUS                     NAMES
8a9b0c1d2e3f   myapp:1.0   "python app.py"   Exited (0) 25 seconds ago  mycontainer
```

### 5. View Logs and Restart Container
```bash
$ docker logs mycontainer
Hello Students!
Welcome to Docker Experiment 9.
This application is running inside a Docker container.

$ docker start -a mycontainer
Hello Students!
Welcome to Docker Experiment 9.
This application is running inside a Docker container.
```

### 6. Clean Up
```bash
$ docker rm mycontainer
mycontainer

$ docker rmi myapp:1.0
Untagged: myapp:1.0
```

---

## Result
A Dockerfile was authored for a simple Python application, a custom Docker image was built and tagged `myapp:1.0`, and the container was executed and tested successfully.
