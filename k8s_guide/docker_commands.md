# Docker Commands

**1. Build a Docker Image**

```bash
docker build -t <username>/<image-name>:<tag> .
```

**2. List Docker Images**

- List all Docker images on your local machine.

```bash
docker images
```

**3. Run a Docker Container**

- Run a container from an image.

```bash
docker run -d -p <host-port>:<container-port> <username>/<image-name>:<tag>
```

- Example:
```bash
docker run -d -p 8080:80 johndoe/my-app:v1
```



**4. Login to Docker Hub**

```bash
docker login
```

**5. Push a Docker Image to Docker Hub**

```bash
docker push <username>/<image-name>:<tag>
```

**6. Pull a Docker Image from Docker Hub**

```bash
docker pull <username>/<image-name>:<tag>
```

**7. Stop a Docker Container**

```bash
docker stop <container-id or container-name>
```
**8. Remove a Docker Container**

```bash
docker rm <container-id or container-name>
```

**9. Remove a Docker Image**

- Remove a Docker image from  local machine.

```bash
docker rmi <image-name>:<tag>
```

**10. View Running Docker Containers**

```bash
docker ps
```

**11. View All Containers (Including Stopped)**

```bash
docker ps -a
```

**12. View Logs of a Running Container**

```bash
docker logs <container-id or container-name>
```

**13. Docker Exec into a Running Container**
- useful for debugging.

```bash
docker exec -it <container-id or container-name> bash
```

**14. Tag a Docker Image**

- Tag an image with a new tag (e.g., for versioning).

```bash
docker tag <source-image>:<tag> <username>/<new-image-name>:<new-tag>
```
