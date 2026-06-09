# Java Application Containerization & CI/CD Pipeline using Jenkins, Docker, and Kubernetes (Kind)

## Overview

This project showcases the containerization, continuous integration, and deployment of a Maven-based Java Spring Boot application using contemporary DevOps practices. The assessment aims to:

- Containerize a Java application using Docker.
- Implement Docker security best practices with a non-root user.
- Create a Kubernetes cluster using Kind.
- Deploy the application to Kubernetes.
- Build a CI/CD pipeline using Jenkins.
- Push container images to Docker Hub.
- Automate the application build and delivery process.

## Architecture

The architecture of the project follows a streamlined workflow:

```plaintext
GitHub Repository
        │
        ▼
     Jenkins
        │
        ▼
   Maven Build
        │
        ▼
 Docker Image Build
        │
        ▼
 Docker Hub Push
        │
        ▼
 Kubernetes (Kind)
```

## Technology Stack

| Component             | Technology      |
|-----------------------|-----------------|
| Source Control        | Git & GitHub    |
| Programming Language  | Java 17         |
| Framework             | Spring Boot     |
| Build Tool            | Maven           |
| Containerization      | Docker          |
| Container Registry    | Docker Hub      |
| CI/CD Tool            | Jenkins         |
| Kubernetes Cluster    | Kind            |
| Operating System      | Ubuntu Linux    |

## Project Structure

The project is organized as follows:

```
java-app/
│
├── src/
│
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
│
├── Dockerfile
├── Jenkinsfile
├── pom.xml
└── README.md
```

## Application Build

The application is built using Maven with the following command:

```shell
mvn clean package -DskipTests
```

**Output:**

- `target/book-api-1.0.0.jar`

## Docker Containerization

### Multi-Stage Docker Build

A multi-stage Dockerfile was implemented to optimize image size and separate build dependencies from runtime dependencies.

- **Build Stage:**
  - Uses a Maven image for compilation.
  - Compiles source code.
  - Generates JAR artifact.

- **Runtime Stage:**
  - Utilizes Eclipse Temurin JRE image.
  - Copies only the generated JAR.
  - Excludes build tools.

**Benefits:**

- Smaller image size.
- Improved security.
- Faster deployment.
- Reduced attack surface.

### Non-Root User Implementation

Container security best practices are followed by creating and using a dedicated non-root user:

```dockerfile
RUN useradd -m appuser
USER appuser
```

**Benefits:**

- Prevents application from running as root.
- Reduces privilege escalation risks.
- Improves container security.

### Docker Image Build

- **Build image:**

  ```shell
  docker build -t saiif/java-app:latest .
  ```

- **Run container:**

  ```shell
  docker run -d -p 8080:8080 saiif/java-app:latest
  ```

- **Health check:**

  ```shell
  curl http://localhost:8080/api/health
  ```

  **Response:**

  ```json
  {
    "status": "healthy"
  }
  ```

## Kubernetes Deployment

### Kind Cluster

A local Kubernetes cluster was created using Kind.

- **Create cluster:**

  ```shell
  kind create cluster --name opstree
  ```

- **Verify cluster:**

  ```shell
  kubectl get nodes
  ```

### Deployment Configuration

The deployment manifest specifies:

- **Replicas:** 2
- **Features:**
  - Rolling updates.
  - Self-healing pods.
  - Container image from Docker Hub.

- **Deploy:**

  ```shell
  kubectl apply -f k8s/
  ```

- **Verify:**

  ```shell
  kubectl get pods
  ```

### Service Configuration

A Kubernetes NodePort service is configured:

- **Features:**
  - Exposes application externally.
  - Maps port 80 to container port 8080.
  - Provides service discovery.

- **Apply service:**

  ```shell
  kubectl apply -f k8s/service.yaml
  ```

- **Verify:**

  ```shell
  kubectl get svc
  ```

## Jenkins CI/CD Pipeline

Jenkins is deployed using Docker with the following pipeline stages:

1. **Source Code Checkout:** Fetch latest source code from GitHub.
2. **Maven Build:** Build Java application.
   
   ```shell
   mvn clean package -DskipTests
   ```

3. **Docker Image Build:** Build container image.
   
   ```shell
   docker build -t saiif/java-app:latest .
   ```

4. **Docker Hub Push:** Authenticate and push image.
   
   ```shell
   docker push saiif/java-app:latest
   ```

5. **Deployment Stage:** Prepare deployment manifests for Kubernetes rollout.

### CI/CD Workflow

```plaintext
Developer Push
       │
       ▼
     GitHub
       │
       ▼
     Jenkins
       │
       ▼
 Maven Build
       │
       ▼
 Docker Build
       │
       ▼
 Docker Hub Push
       │
       ▼
 Kubernetes Deployment
```

## Security Best Practices Implemented

### Docker Security

- Multi-stage builds.
- Non-root user.
- Minimal runtime image.
- Reduced attack surface.

### Jenkins Security

- Docker Hub credentials stored in Jenkins Credentials Manager.
- No hardcoded secrets in pipeline.

### Kubernetes Security

- Replica-based deployment.
- Declarative manifests.
- Rolling updates.

## Challenges Encountered

### Docker Permission Issue in Jenkins

**Issue:** Permission denied while trying to connect to `docker.sock`.

**Resolution:**

- Mounted Docker socket.
- Added Jenkins user to Docker group.

### Jenkinsfile Parsing Error

**Issue:** Unexpected char: '`'.

**Resolution:** Removed accidental markdown syntax from Jenkinsfile.

### Kubernetes Connectivity from Jenkins Container

**Issue:** Connection refused to Kubernetes API server.

**Cause:** Jenkins running inside Docker container; Kind API server bound to localhost.

**Resolution:** Kubernetes deployment verified manually from host machine.

## Results

Successfully achieved:

- Maven-based Java application build.
- Docker multi-stage image creation.
- Non-root container execution.
- Docker Hub image publishing.
- Kubernetes deployment using Kind.
- Jenkins CI pipeline implementation.
- End-to-end DevOps workflow automation.

## Conclusion

This assessment demonstrates practical DevOps skills including application containerization, CI/CD pipeline implementation, Docker image management, Kubernetes deployments, and infrastructure automation. The solution adheres to industry-standard practices such as multi-stage Docker builds, non-root containers, automated CI pipelines, and Kubernetes-based application deployment.