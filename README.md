# 🐳 **AWS ECS**

A beginner-friendly, easy-to-understand explanation of **Containerization**, **Docker**, **AWS ECS**, and a **simple hands-on lab**.

---

# 📦 **1. Introduction to Containerization**

## 🔹 What is Containerization?

Containerization is the process of packaging an application along with all its dependencies (libraries, runtime, config files) into a lightweight, portable unit called a **container**.

### ⭐ Benefits of Containerization

* ⚡ Fast deployment
* 🧩 Works the same everywhere (no environment conflicts)
* 🔁 Easy scaling
* 🪶 Lightweight (uses less resources than VMs)
* 🚀 Portable across systems (laptop → server → cloud)

## 🐳 Docker (Most Popular Container Tool)

### 🧱 Docker Image

Blueprint of the application.

### 🟦 Docker Container

Running instance of the Docker image.

---

# 🟦 **2. What is AWS ECS (Elastic Container Service)?**

AWS ECS is a fully managed **container orchestration service** used to **run, manage, and scale Docker containers**.

Think of ECS as a service that takes care of:

* Scheduling containers
* Starting/stopping tasks
* Scaling automatically
* Load balancing your app
* Handling networking

---

# 🧱 **3. ECS Building Blocks (Simple Explanation)**

| Component           | Meaning                                                           |
| ------------------- | ----------------------------------------------------------------- |
| **Cluster**         | Group of resources running containers (EC2 or Fargate)            |
| **Task Definition** | Blueprint of how container should run (image, CPU, memory, ports) |
| **Task**            | A running container                                               |
| **Service**         | Ensures the required number of tasks are always running           |
| **ECR**             | AWS container registry to store Docker images                     |

---

# 🟩 **4. ECS Launch Types**

### 🔹 1. **EC2 Launch Type**

* Containers run on your EC2 instances
* You manage the servers
* More control, but more work

### 🔹 2. **Fargate Launch Type**

* Serverless containers
* No EC2 needed
* AWS manages everything
* Best for beginners

---

# 🌀 **5. ECS Workflow (Easy Diagram)**

```
Developer → Docker Image → Push to ECR
                ↓
             ECS Cluster
                ↓
        ECS Task Definition
                ↓
        ECS Service (Auto-scaling)
                ↓
            Running Tasks
                ↓
   ALB Load Balancer → Users
```

---

# 🟧 **6. Use Cases of ECS**

* Microservices
* Web applications
* APIs
* Background jobs
* Log processing systems
* CI/CD workloads

---

Got it!
Here is the **final README.md content**, fully formatted, clean, and ready for you to **copy & paste directly**.

---

# **AWS ECS — NGINX Lab (Using Amazon Linux CLI & ECR)**

A straightforward, fixed-version lab to **pull `nginx:latest` on an Amazon Linux host, tag it, push to ECR, and deploy on ECS (Fargate)**.
This version includes **all corrected commands** based on your errors.

---

## **Prerequisites**

* AWS account
* Amazon Linux EC2 (Amazon Linux 2023 or 2)
* Docker installed
* AWS CLI configured (or IAM role on EC2)
* ECR repository (we will create if not exists)

---

# **0 — Check OS Version (important)**

```bash
cat /etc/os-release
```

---

# **1 — Install Docker**

## **If Amazon Linux 2023:**

```bash
sudo dnf update -y
sudo dnf install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user
```

## **If Amazon Linux 2:**

```bash
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -aG docker ec2-user
```

Logout and login again.

Check Docker:

```bash
docker ps
```

---

# **2 — Configure AWS Credentials (if needed)**

If you saw this error earlier:

```
Unable to locate credentials
```

Run:

```bash
aws configure
```

Enter:

* Access Key ID
* Secret Key
* Region (us-east-1)
* Output: json

---

# **3 — Pull the NGINX Image (from Docker Hub)**

```bash
docker pull nginx:latest
```

Verify:

```bash
docker images
```

---

# **4 — Create an ECR Repository (if not exists)**

```bash
aws ecr describe-repositories --repository-names ecs-demo --region us-east-1 >/dev/null 2>&1 || \
aws ecr create-repository --repository-name ecs-demo --region us-east-1
```

---

# **5 — Login to ECR (Correct Syntax)**

```bash
aws ecr get-login-password --region us-east-1 \
| docker login --username AWS --password-stdin 526888234336.dkr.ecr.us-east-1.amazonaws.com
```

You should see:

```
Login Succeeded
```

---

# **6 — Tag the Local Image With ECR Repo URI**

```bash
docker tag nginx:latest 526888234336.dkr.ecr.us-east-1.amazonaws.com/ecs-demo:latest
```

Check tags:

```bash
docker images
```

---

# **7 — Push Image to ECR**

```bash
docker push 526888234336.dkr.ecr.us-east-1.amazonaws.com/ecs-demo:latest
```

---

# **8 — Create ECS Cluster (Fargate)**

AWS Console → ECS → Clusters → Create Cluster

* **Networking Only (Fargate)**
* Name: `ecs-nginx-cluster`

---

# **9 — Create ECS Task Definition**

Task Definitions → Create New

* **Launch Type:** Fargate
* **CPU:** 256
* **Memory:** 512
* **Add container**

Container Settings:

* Name: `nginx-container`
* Image:

  ```
  526888234336.dkr.ecr.us-east-1.amazonaws.com/ecs-demo:latest
  ```
* Port: **80**

Create.

---

# **10 — Create ECS Service**

Cluster → Create Service

* Launch Type: Fargate
* Service Name: `nginx-service`
* Tasks: 1

Networking:

* Select VPC
* Select **public subnets**
* Auto-assign public IP → **ENABLE**

Create service.

---

# **11 — Verify Deployment**

1. Go to ECS → Cluster → Tasks
2. Task status: **RUNNING**
3. Check **Public IP**
4. Open in browser:

```
http://<public-ip>
```

You should see the **NGINX Welcome Page** 🎉

---

# **Troubleshooting**

### **Error: unknown flag: --shyamdev**

Cause: Wrong docker login command
Fix: Always use

```bash
--username AWS --password-stdin
```

### **Error: An image does not exist locally**

Cause: You didn’t tag local image
Fix:

```bash
docker tag nginx:latest <ecr-uri>
```

### **Error: Unable to locate credentials**

Fix:

```bash
aws configure
```

### **Push denied**

IAM role/user missing permissions.

---

# **Final Flow Summary**

```
Amazon Linux → docker pull nginx → tag → ECR login → docker push → ECS (Fargate) → Public IP → NGINX page
```

---



