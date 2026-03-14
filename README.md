# Word Counter

Hi 👋

This is a word counter built with HTML, CSS and JavaScript. It simply counts the number of words you have in your text.


## How to use

Although this is a very simple frontend application, for practice, I created a `Dockerfile` to create an image for this application. It was also pushed to Docker hub via github Actions. 

This process makes setting this application up very simple. The following steps can be used to set up this project on your local machine.

- Clone the repository
- Build the Docker image
  ```
  Docker build -t wordcounter .
  ```
- Run the Dockerfile
  ```
  docker run -p 80:80 wordcounter:latest
  ```
 - On your browser, view `localhost`

**Note:**

You have write the text in the text area form and click on the button to count the text. This project's main feature is built using one of JavaScript's string manipulation technique, the `split()` method.

## Project view

![word counter view](/wordcount.JPG)

## Background

My IT defense required that I write an essay which would be 250 words long. As a fun idea, I decided to make a word counter which would simply help me count the length of the text. 

## Contribution

You can go through the project and it's various files and raise issues where you find bugs, or want to improve.

## Credits

The inspiration of this project is from Rivers state university, computer science dept.



# 🚀 Jenkins CI/CD Pipeline Deployment to AWS EKS

This project demonstrates a complete **DevOps CI/CD pipeline** that automatically builds a Docker image, pushes it to AWS, and deploys it to Kubernetes using Jenkins.

---

# 🏗 Architecture

```text
GitHub
   ↓
Jenkins Pipeline
   ↓
Docker Build
   ↓
Push Image → AWS ECR
   ↓
Deploy → AWS EKS
   ↓
Expose via LoadBalancer
```

---

# ⚙️ Prerequisites

Ensure the following tools are installed on the Jenkins server:

* Docker
* AWS CLI
* kubectl
* eksctl
* Git
* Jenkins

---

# ☁️ AWS Setup

## Configure AWS CLI

```bash
aws configure
```

Verify access:

```bash
aws sts get-caller-identity
```

---

# ☸️ Create EKS Cluster

```bash
eksctl create cluster \
--name jenkins-eks-cluster \
--region ap-south-1 \
--nodegroup-name workers \
--node-type t3.micro \
--nodes 2
```

Verify nodes:

```bash
kubectl get nodes
```

---

# 📦 Create ECR Repository

```bash
aws ecr create-repository \
--repository-name jenkins-eks-app \
--region ap-south-1
```

Verify repository:

```bash
aws ecr describe-repositories
```

---

# 🔑 Jenkins Credentials Setup

In Jenkins:

```
Manage Jenkins
→ Credentials
→ Add Credentials
```

Add AWS credentials with ID:

```
AWS-CREDS
```

---

# 📂 Create Jenkins Pipeline

Create a new pipeline job.

```
New Item → Pipeline
```

Pipeline configuration:

```
Pipeline script from SCM
```

Repository:

```
https://github.com/Rahul786007/Word-counter.git
```

Branch:

```
main
```

Script Path:

```
Jenkinsfile
```

---

# 🚀 Run Jenkins Pipeline

Trigger build:

```
Build Now
```

Pipeline stages executed:

```
Checkout SCM
Configure
Building image
Pushing to ECR
K8S Deploy
Get Service URL
```

---

# 📊 Verify Deployment

Check pods:

```bash
kubectl get pods
```

Check services:

```bash
kubectl get svc
```

Example output:

```
word-counter-service   LoadBalancer
```

Retrieve application URL:

```bash
kubectl get svc word-counter-service \
-o jsonpath='{.status.loadBalancer.ingress[0].hostname}'
```

---

# 🌐 Access Application

Open the LoadBalancer URL in the browser:

```
http://<load-balancer-url>
```

---

# 📈 Scaling Nodegroup (If Pods Pending)

If pods remain pending due to node limits:

```bash
eksctl scale nodegroup \
--cluster jenkins-eks-cluster \
--name workers \
--nodes 3 \
--nodes-max 3 \
--region ap-south-1
```

Verify nodes:

```bash
kubectl get nodes
```

---

# 🧹 Cleanup (Avoid AWS Charges)

Delete the cluster:

```bash
eksctl delete cluster \
--name jenkins-eks-cluster \
--region ap-south-1
```

Delete ECR repository:

```bash
aws ecr delete-repository \
--repository-name jenkins-eks-app \
--region ap-south-1 \
--force
```

---

# 🎯 Learning Outcome

This project demonstrates:

* CI/CD pipeline automation using Jenkins
* Docker image lifecycle management
* AWS ECR integration
* Kubernetes deployment on AWS EKS
* End-to-end DevOps workflow
