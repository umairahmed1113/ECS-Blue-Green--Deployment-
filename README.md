# ECS EC2 Blue-Green Deployment (AWS)

This guide explains how to perform a **Blue-Green deployment using Amazon ECS (EC2 launch type)** with a single ECR repository.

---

## 🧱 Architecture Overview

* **ECR Repository:** `blue-green`
* **ECS Cluster:** EC2-based cluster
* **Deployment Strategy:** Blue/Green
* **Load Balancer:** Application Load Balancer (ALB)
* **Target Groups:**

  * `blue` (current/live)
  * `green` (new version)

---

## 🚀 Step 1: Push Blue Version to ECR

```bash
docker build -t blue-green:blue .
docker tag blue-green:blue <account-id>.dkr.ecr.<region>.amazonaws.com/blue-green:blue
docker push <account-id>.dkr.ecr.<region>.amazonaws.com/blue-green:blue
```

📸 ECR Repository:
<img width="945" height="322" alt="image" src="https://github.com/user-attachments/assets/168d742a-6fc4-45f6-890a-f51c2ac45bac" />

---

## 🏗️ Step 2: Create ECS EC2 Cluster

* Go to **ECS → Clusters → Create Cluster**
* Choose **EC2**
* Select instance type and capacity

📸 Cluster Infrastructure:
<img width="1621" height="686" alt="image" src="https://github.com/user-attachments/assets/5d0846dc-ab1c-42f2-acd9-9bfcd623b6d0" />


---

## 📦 Step 3: Create Task Definition

* Launch type: EC2
* Container image: ECR blue image
* Port: 80

{
  "family": "blue-green-task",
  "containerDefinitions": [
    {
      "name": "blue-green",
      "image": "id.dkr.ecr.us-east-1.amazonaws.com/blue-green@sha256:c5782dac1f9e03f05af415441c04775df17acb54c6a7b1a6de54bd7646c766e1",
      "essential": true,
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "appProtocol": "http"
        }
      ]
    }
  ],
  "executionRoleArn": "arn:aws:iam::id:role/ecsTaskExecutionRole",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["EC2"],
  "cpu": "1024",
  "memory": "1024",
  "runtimePlatform": {
    "cpuArchitecture": "X86_64",
    "operatingSystemFamily": "LINUX"
  }
}


---

## ⚙️ Step 4: Create ECS Service (Blue Deployment)

* Select task definition
* Service name

### Deployment Strategy

* Choose **Blue/Green**
* Set **Bake time (e.g., 2 minutes)**

📸 Deployment Configuration:
<img width="1628" height="748" alt="image" src="https://github.com/user-attachments/assets/26fb295e-d86c-4f3a-b56e-6a0d626f2063" />
<img width="1628" height="748" alt="image" src="https://github.com/user-attachments/assets/4fbb2a3f-c107-46bf-81e9-7a7483277f62" />


---

## 🌐 Step 5: Configure Networking

* Select VPC
* Select subnets

---

## ⚖️ Step 6: Configure Load Balancer

* Choose Application Load Balancer
* Listener: Port 80 (HTTP)

📸 Load Balancer:
<img width="1628" height="748" alt="image" src="https://github.com/user-attachments/assets/dbffe856-ccd8-4834-a148-1c2e792ebe22" />


### Target Groups

* Create:

  * `blue`
  * `green`

📸 Target Groups:
<img width="1628" height="748" alt="image" src="https://github.com/user-attachments/assets/79cc367b-c8e2-495f-8e6c-68e958c728a4" />


---

## ✅ Step 7: Verify Blue Deployment

* Copy Load Balancer DNS
* Open in browser

📸 Blue Environment:
<img width="1913" height="930" alt="image" src="https://github.com/user-attachments/assets/ee52c4a9-2d96-4be2-bb62-25ba25e6b173" />


---

## 🔄 Step 8: Deploy Green Version
In same ecr repo in which blue is deployed 
```bash
docker build -t blue-green:green .
docker tag blue-green:green <account-id>.dkr.ecr.<region>.amazonaws.com/blue-green:green
docker push <account-id>.dkr.ecr.<region>.amazonaws.com/blue-green:green
```

---

## 🔁 Step 9: Update Service

* Create new task revision
Just pick a new image from ECR and update the task one to task two, don't change anything; just change the task version
* Update service
  In service, just update the service from task one to task two
---

## 📊 Step 10: Observe Deployment

* Two tasks running:

  * Blue
  * Green

📸 Health & Metrics:
<img width="1591" height="537" alt="image" src="https://github.com/user-attachments/assets/9d9da0bb-2ce5-41ba-99dd-1c4db11c354d" />


📸 Tasks:
<img width="1752" height="799" alt="image" src="https://github.com/user-attachments/assets/6c8d555d-0752-46a8-b0d4-d035e41cf0e4" />


---

## 🔀 Step 11: Traffic Shift

* Traffic moves from Blue → Green
* After bake time → Blue terminated

📸 Green Environment:
<img width="1912" height="926" alt="image" src="https://github.com/user-attachments/assets/88e53c0d-eca1-4146-8376-ba0f9a11ed79" />


📸 Final State:
<img width="1895" height="703" alt="image" src="https://github.com/user-attachments/assets/a99fcbeb-b8e6-4b60-b334-acbe7cf7f35f" />
<img width="1895" height="703" alt="image" src="https://github.com/user-attachments/assets/27536450-f6fb-4a43-8bc5-63dbc867d2ca" />


---

## 🟢 Final Result

* Zero downtime deployment
* Green becomes production

---

## ⚠️ Notes

* Ensure security groups allow port 80
* Configure health checks correctly
* Use consistent container ports

---

## 🎉 Result

Blue-Green deployment successfully implemented 🚀

This is manually done, but with the help of code deploy, we can automate it 
