# unisecure-trivy

A demonstration project showcasing Docker security best practices using Trivy vulnerability scanner.

## Overview

This project contains a simple Node.js web application with two Docker configurations:
- `Dockerfile` - An intentionally insecure Docker configuration for demonstration purposes
- `Dockerfile.secured` - A secured version following Docker security best practices

## Application

The application is a basic Node.js HTTP server that serves "Hello, World!" on port 3000.

## Security Comparison

### Insecure Dockerfile Issues:
- Uses a generic Node.js image (larger attack surface)
- Copies all files before dependency installation (inefficient caching)
- Runs as root user
- No dependency verification
- References incorrect entry point (`server.js` instead of `index.js`)

### Secured Dockerfile Improvements:
- Uses Alpine-based Node.js image (smaller, more secure)
- Optimized layer caching by copying package files first
- Uses `npm ci` for clean, reproducible installs
- Runs as non-root user (`node`)
- Proper file ownership management


### Security Scanning with Trivy

#### Local Scanning

First, install Trivy on your local machine:

```bash
# macOS (using Homebrew)
brew install aquasecurity/trivy/trivy

# Linux (using curl)
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin

# Or using Docker
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  -v $PWD:/tmp aquasec/trivy:latest
```




#### Scanning Dockerfiles for Misconfigurations

```bash
# Scan Dockerfile for security misconfigurations
trivy config Dockerfile

# Scan the secured Dockerfile
trivy config Dockerfile.secured

```

#### Automated Workflow

This project includes a CI/CD workflow that automatically:
1. **Builds** the Docker image
2. **Scans** the image using Trivy for vulnerabilities
3. **Pushes** to Docker registry only when no critical issues are found
4. **Skips** the push step if high/critical vulnerabilities are detected

**Important Note:** Even when the Dockerfile is not secured and contains vulnerabilities, the CI/CD job will still pass successfully. However, only the pushing task to the Docker registry will be skipped. This approach allows developers to see the build and scan results without failing the entire pipeline, while ensuring that vulnerable images are never deployed to production environments.

## Learning Objectives

This project demonstrates:
- Common Docker security vulnerabilities
- Best practices for secure container builds
- How to use Trivy for vulnerability scanning
- The impact of different base images on security posture
