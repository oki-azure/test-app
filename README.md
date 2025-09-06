# CI/CD Pipeline with GitHub Actions and Self-Hosted Runner

This project demonstrates a basic CI/CD pipeline using **GitHub Actions** and a **self-hosted runner** on an Ubuntu VM. The pipeline runs placeholder tests, builds a Docker image, and deploys it locally on the same machine.

---

## Pipeline Overview

The workflow is triggered on every push to the `main` branch and includes the following steps:

1. **Checkout Code**  
   Retrieves the latest commit from the repository.

2. **Run Tests**  
   Executes placeholder test logic (can be replaced with real test commands).

3. **Build Docker Image**  
   Uses a minimal `Dockerfile` to build a container image tagged as `test-app:latest`.

4. **Deploy Locally**  
   Stops and removes any existing container named `app`, then runs the new image on port 80.

---

## Project Structure
test-app/ ├── Dockerfile └── .github/ └── workflows/ └── ci-cd.yml


### Sample Dockerfile

```Dockerfile
FROM alpine
CMD ["echo", "Hello from your self-hosted CI/CD pipeline"]
```

### Security Vulnerability & Fix
##  Vulnerability: Hardcoded Secrets
In early versions of the pipeline, credentials (e.g., Docker Hub username/password) were hardcoded into the workflow file. This exposes sensitive data to version control and collaborators.
#   Fix: GitHub Secrets
Secrets were moved to GitHub’s encrypted secrets store and referenced securely in the workflow using:
```Yaml
with:
  username: ${{ secrets.DOCKER_USERNAME }}
  password: ${{ secrets.DOCKER_PASSWORD }}
```

For the self-hosted runner, no external secrets are required since deployment occurs locally.

###  Self-Hosted Runner Setup
The runner is installed on the same Ubuntu VM that serves as the staging environment. It listens for jobs from GitHub and executes them directly.

Key Commands:
```Bash
## Download and configure runner
curl -o actions-runner-linux-x64.tar.gz -L https://github.com/actions/runner/releases/download/v2.x.x/actions-runner-linux-x64.tar.gz
tar xzf actions-runner-linux-x64.tar.gz
./config.sh --url https://github.com/YOUR_USERNAME/test-app --token YOUR_TOKEN

## Start runner
./run.sh
```

### Deployment Verification
After a successful run:
```Bash
docker ps        # Check running containers
docker logs app  # View container output
```

Expected output:
```
Hello from the self-hosted CI/CD pipeline
```

### Notes
- This pipeline is intentionally minimal for demonstration purposes.
- It can be extended with real test suites, multi-stage builds, and external deployment targets.
- The self-hosted runner eliminates SSH and networking issues common with cloud-based runners.

### Author: Kelvin Kwaku Agbozo
