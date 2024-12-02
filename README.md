name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout Code
      uses: actions/checkout@v3

    - name: Log in to DockerHub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and Push Docker Image
      run: |
        docker build -t <your-docker-registry>/wisecow:latest .
        docker push <your-docker-registry>/wisecow:latest

  deploy:
    runs-on: ubuntu-latest
    needs: build-and-push
    steps:
    - name: Set up kubectl
      uses: azure/setup-kubectl@v3
      with:
        version: 'latest'

    - name: Deploy to Kubernetes
      run: |
        kubectl apply -f wisecow-deployment.yaml
        kubectl apply -f wisecow-service.yaml
