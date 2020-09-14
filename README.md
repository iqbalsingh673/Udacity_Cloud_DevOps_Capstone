# Udacity_Cloud_DevOps_Capstone

Project Overview
Use skills and knowledge which were developed throughout the Cloud DevOps Nanodegree program to develop a CI/CD pipeline for micro services applications with either blue/green deployment
In this project, I have automated a web deployment from a code commit (Git) to an Amazon EKS cluster that is load balanced with blue/green deployment.
These tasks were performed through a Jenkins pipeline running on an EC2 instance.

Continuous Integration will include checking the GitHub repo for new commits and perform typographical checking (aka “linting”) on the HTML.
Continuous Deployment will include pushing the built Docker container to the DockerHub repository. Next I have deployed these Docker container(s) to a small Kubernetes cluster using AWS EKS (AWS Kubernetes as a Service).

The pipeline steps include:

Linting the code
Building docker image from Dockerfile
Pushing docker image to Dockerhub
Deploying image to Kubernetes Cluster
Deploying the another version of app through Rolling Deployment Strategy
