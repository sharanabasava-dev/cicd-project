# CI/CD Pipeline Project using GitLab, Docker, and AWS EC2

## Project Overview
This project demonstrates a complete **End-to-End CI/CD pipeline** where code pushed to the repository automatically builds a Docker image, pushes it to Docker Hub, and deploys the application to an AWS EC2 instance.

The pipeline is implemented using **GitLab CI/CD**.

---

## Architecture

Developer → GitLab Repository → GitLab CI/CD Pipeline → Docker Build → Docker Hub → AWS EC2 → Running Container

---

## Tools & Technologies

- GitLab (Source Code Management)
- GitLab CI/CD (Pipeline Automation)
- Docker (Containerization)
- Docker Hub (Image Registry)
- AWS EC2 (Deployment Server)
- Python Flask (Application)

---

## Project Structure
cicd-project
│
├── app/
│ └── app.py
│
├── tests/
│ └── test_app.py
│
├── Dockerfile
├── requirements.txt
├── .gitlab-ci.yml
├── .gitignore
└── README.md


---

## CI/CD Pipeline Workflow

1. Developer pushes code to GitLab repository.
2. GitLab CI/CD pipeline is automatically triggered.
3. Docker image is built using the Dockerfile.
4. The Docker image is pushed to Docker Hub.
5. GitLab pipeline connects to AWS EC2 via SSH.
6. EC2 pulls the latest Docker image.
7. Old container is stopped and removed.
8. New container is started with the latest image.
9. Application becomes live.

---

## GitLab Pipeline Stages

- Build Docker Image
- Push Image to Docker Hub
- Deploy Container to AWS EC2

---

## Docker Build Command

Build Docker Image:
docker build -t cicd-app .
Run Container: docker run -d -p 5000:5000 cicd-app
Access application locally:http://localhost:5000

---

## Application Deployment

After CI/CD pipeline deployment, the application is available at:
http://13.201.23.52:5000

---

## GitLab CI/CD Pipeline File (.gitlab-ci.yml)
stages:

build

push

deploy

variables:
IMAGE_NAME: sharana123/cicd-app

build:
stage: build
script:
- docker build -t $IMAGE_NAME:latest .
only:
- main

push:
stage: push
script:
- docker login -u $DOCKER_HUB_USER -p $DOCKER_HUB_PASS
- docker push $IMAGE_NAME:latest
only:
- main

deploy:
stage: deploy
image: alpine:latest
before_script:
- apk add --no-cache openssh-client
script:
- ssh $EC2_USER@$EC2_HOST "
docker pull $IMAGE_NAME:latest &&
docker stop cicd-app || true &&
docker rm cicd-app || true &&
docker run -d -p 5000:5000 --name cicd-app $IMAGE_NAME:latest
"
only:
- main

---

## Security

Sensitive information such as:

- Docker Hub username and password
- EC2 SSH private key
- EC2 host IP

are stored securely in **GitLab CI/CD Variables**.

---

## Future Improvements

- Kubernetes deployment (AWS EKS)
- Infrastructure provisioning using Terraform
- Monitoring using Prometheus and Grafana
- Nginx reverse proxy with HTTPS
- Auto scaling using Kubernetes

---

## Author

Sharana Basava  
DevOps Engineer
