<img width="945" height="322" alt="image" src="https://github.com/user-attachments/assets/168d742a-6fc4-45f6-890a-f51c2ac45bac" /># ECS EC2 Blue-Green Deployment (AWS)

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

📸 Task Definition Selection:
![Task](attachment:1d6cb158-8b52-4ce1-97cb-56b0fdd81e11.png)

---

## ⚙️ Step 4: Create ECS Service (Blue Deployment)

* Select task definition
* Service name

### Deployment Strategy

* Choose **Blue/Green**
* Set **Bake time (e.g., 2 minutes)**

📸 Deployment Configuration:
![Deployment](attachment:93b035cb-3fc7-4a2d-a04f-82a657d025dd.png)

---

## 🌐 Step 5: Configure Networking

* Select VPC
* Select subnets

---

## ⚖️ Step 6: Configure Load Balancer

* Choose Application Load Balancer
* Listener: Port 80 (HTTP)

📸 Load Balancer:
![LB](attachment:70329506-108d-478a-b972-953e06761885.png)

### Target Groups

* Create:

  * `blue`
  * `green`

📸 Target Groups:
![TG](attachment\:d26ffc13-6ddf-4259-a3eb-e61458e482f6.png)

---

## ✅ Step 7: Verify Blue Deployment

* Copy Load Balancer DNS
* Open in browser

📸 Blue Environment:
![Blue](attachment\:c3314e1c-e429-421f-a0fd-c8690a4eed9d.png)

---

## 🔄 Step 8: Deploy Green Version

```bash
docker build -t blue-green:green .
docker tag blue-green:green <account-id>.dkr.ecr.<region>.amazonaws.com/blue-green:green
docker push <account-id>.dkr.ecr.<region>.amazonaws.com/blue-green:green
```

---

## 🔁 Step 9: Update Service

* Create new task revision
* Update service

📸 Service Update:
![Update](attachment\:ed8f7a94-0456-4b2a-84be-d8ad4d8308cf.png)

---

## 📊 Step 10: Observe Deployment

* Two tasks running:

  * Blue
  * Green

📸 Health & Metrics:
![Metrics](attachment\:d05cf557-1573-4c68-bfdc-0821d3f3c609.png)

📸 Tasks:
![Tasks](attachment:33757972-d726-47b2-b762-d3b6e2f4722f.png)

---

## 🔀 Step 11: Traffic Shift

* Traffic moves from Blue → Green
* After bake time → Blue terminated

📸 Green Environment:
![Green](attachment\:fff6ec89-5822-438d-a0d4-19ccd392f0fc.png)

📸 Final State:
![Final](attachment:1f3a68a4-7047-49c1-b053-ae9b6b4ed064.png)

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
