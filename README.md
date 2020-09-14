# Udacity_Cloud_DevOps_Capstone

### Project Overview
Use skills and knowledge which were developed throughout the Cloud DevOps Nanodegree program to develop a CI/CD pipeline for micro services applications with either blue/green deployment
In this project, I have automated a web deployment from a code commit (Git) to an Amazon EKS cluster that is load balanced with blue/green deployment.
These tasks were performed through a Jenkins pipeline running on an EC2 instance.

Continuous Integration will include checking the GitHub repo for new commits and perform typographical checking (aka “linting”) on the HTML.
Continuous Deployment will include pushing the built Docker container to the DockerHub repository. Next I have deployed these Docker container(s) to a small Kubernetes cluster using AWS EKS (AWS Kubernetes as a Service).

##### The pipeline steps include:

 1.   Lint HTML
 2.   Build Docker images for Blue and Green deployments
 3.   Push docker images to Dockerhub
 4.   Set cluster as current context
 5.   Deploy blue container
 6.   Deploy green container
 7.   Create service in the cluster, redirect to Blue
 8.   Prompt for traffic redirection (Proceed/Abort)
 9.   If Proceed, create service in the cluster and redirect to Green


### Environment Setup Requirements:
  - Install Jenkins on an Amazon EC2 Instance
  - Install Jenkins Plugins
  - Blue Ocean
  - Pipeline AWS plugin
  - Credentials for AWS and Docker Hub 
  - Install linter
  - Install and configure AWS CLI
  - Install eksctl
  - Install and configure kubectl
  - Install Docker
    
    Reference - https://medium.com/@andresaaap/jenkins-pipeline-for-blue-green-deployment-using-aws-eks-kubernetes-docker-7e5d6a401021

### Steps
  1. Install Jenkins and all the necessary plugins in the EC2 Instance
  2. Create EKS Cluster by building pipeline using `create_cluster/Jenkinsfile`
  3. Build, Push and Deploy container by building pipeline using `Jenkinsfile`
 
### Troubleshooting
1. Authentication required - Unauthorized or access denied

    ```sh
        kubectl version
        Client Version: version.Info{... }
        Error from server (Forbidden): ...
        Authentication required
        <!--
        You are authenticated as: anonymous
        ...
    ```

    >Solution - Update your kubeconfig file to use an IAM role
    ```sh
       aws --region region-code eks update-kubeconfig --name cluster_name --role-arn arn:aws:iam::aws_account_id:role/role_name
    ```
   
    Reference - https://docs.aws.amazon.com/eks/latest/userguide/troubleshooting.html#unauthorized
2. Docker Permission denied in Jenkins Build pipeline logs:
    ```sh
    + docker login -u **** -p ****
    Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.40/auth: dial unix /var/run/docker.sock: connect: permission denied
    ```
    >Solution - Grant Permission Using:
    ```sh
        sudo chmod 666 /var/run/docker.sock
    ``` 
    
    Reference - https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket