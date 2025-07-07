🚀 DevOps vProfile Project on AWS (ECR + EKS + CodeBuild + CloudFormation)
This project demonstrates a full CI/CD pipeline for deploying the containerized vProfile web application on Amazon EKS, using:

Amazon ECR for storing Docker images

AWS CodeBuild for automated build and deployment

Amazon EKS for running the app in Kubernetes

CloudFormation for provisioning backend infrastructure

📦 Tech Stack
Layer	Tool/Service
Source Code	GitHub (https://github.com/nedakhodabakhshi/Devops-Vprofile-EKS)
Build	AWS CodeBuild
Container	Docker + Amazon ECR
Orchestration	Amazon EKS (Kubernetes)
Infra-as-Code	AWS CloudFormation (for backend)
App Server	Tomcat (vProfile WAR app)
Language	Java 11

🛠️ Project Architecture
pgsql
Copy
Edit
 GitHub (Source)
       |
       ↓
 AWS CodeBuild → Builds Docker Image → Pushes to ECR
       ↓
  Deploys to Amazon EKS (Kubernetes)
       ↓
 LoadBalancer → Exposes App via External IP
       ↓
    App UI on /login (Tomcat WAR)
✅ Key AWS Resources
🔹 ECR Repository
Docker image:
557690612191.dkr.ecr.us-east-1.amazonaws.com/myrepo:latest

🔹 EKS Cluster
Name: mycluster

Region: us-east-1

2 Node EC2 Worker Nodes

Status: ACTIVE

🔹 Load Balancer
Automatically created by Kubernetes Service of type LoadBalancer.
Public URL:

http
Copy
Edit
http://a15452df91cf048ebbebd16827ca8e6a-655375236.us-east-1.elb.amazonaws.com/login
📄 CloudFormation
Used to provision backend resources like:

RDS MySQL

Memcached

RabbitMQ

Security groups and networking settings are configured to allow EC2 and services to communicate properly.

🔧 Dockerfile
dockerfile
Copy
Edit
FROM public.ecr.aws/docker/library/tomcat:9.0-jdk11-openjdk-slim
RUN rm -rf /usr/local/tomcat/webapps/*
COPY target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD ["catalina.sh", "run"]
🧪 Kubernetes Manifests
deployment.yaml
Deploys 2 pods with image pulled from ECR

Exposes port 80

service.yaml
yaml
Copy
Edit
type: LoadBalancer
ports:
  - port: 80
    targetPort: 80
selector:
  app: my-app
🧱 buildspec.yml (AWS CodeBuild)
Highlights:

yaml
Copy
Edit
pre_build:
  commands:
    - aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 557690612191.dkr.ecr.us-east-1.amazonaws.com
build:
  commands:
    - docker build -t 557690612191.dkr.ecr.us-east-1.amazonaws.com/myrepo:latest .
    - docker push 557690612191.dkr.ecr.us-east-1.amazonaws.com/myrepo:latest
post_build:
  commands:
    - aws eks update-kubeconfig --region us-east-1 --name mycluster
    - kubectl apply -f deployment.yaml
    - kubectl apply -f service.yaml
    - kubectl get svc
📷 Project Screenshots
Description	Screenshot
AWS CLI Auth	
Cluster Active	
CodeBuild Success	
Web App Running	
LoadBalancer Created	

🔐 Environment Variables in CodeBuild
Make sure the following variables are set:

AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY

DOCKER_USERNAME

DOCKER_PASSWORD

✅ How to Access the App
After CodeBuild succeeds, access the app via:

bash
Copy
Edit
http://<your-lb-dns>/login
Example:
http://a15452df91cf048ebbebd16827ca8e6a-655375236.us-east-1.elb.amazonaws.com/login





