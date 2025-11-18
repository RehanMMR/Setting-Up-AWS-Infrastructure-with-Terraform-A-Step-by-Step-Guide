# 🌐 AWS Web Infrastructure Using Terraform

This project provisions a complete AWS web infrastructure using **Terraform**, including:

- Custom VPC  
- Two public subnets  
- Internet Gateway  
- Route table + associations  
- Security group  
- Two EC2 instances (web servers)  
- Application Load Balancer  
- Target group + attachments  
- Listener  
- S3 bucket  
- Output (ALB DNS)

This architecture provides **high availability**, **internet accessibility**, and **load balancing** across multiple availability zones.

---

## 🖼️ Architecture Diagram

## Terraform Infrastructure Setup on AWS

<img width="611" height="481" alt="VPC Architecture" src="sandbox:/mnt/data/Screenshot%202025-11-18%20153614.png" />


---

# 📘 Project Overview

This Terraform configuration deploys a **production-style web infrastructure** in AWS:

- The **ALB** receives traffic from the internet.
- It forwards requests to **two EC2 instances** in separate subnets.
- Each subnet is inside a custom **VPC** with public access.
- A **security group** secures the servers.
- An **S3 bucket** is also created for storage.
- Outputs include the **load balancer DNS** for accessing the application.

This mirrors real-world deployments used for scalable, fault-tolerant applications.

---

# 🏗️ Infrastructure Components Explained (Step-by-Step)

## 1️⃣ VPC (Virtual Private Cloud)
We create a custom VPC that provides an isolated network inside AWS.

```hcl
resource "aws_vpc" "myvpc" {
  cidr_block = var.cidr
}

````

* Helps define IP ranges
* Forms the foundation for networking components
* Refer the userdata fil, you can find the CIDR block

---

## 2️⃣ Public Subnets

Two public subnets in **us-east-1a** and **us-east-1b** ensure high availability.

```hcl
cidr_block = "10.0.0.0/24"
cidr_block = "10.0.1.0/24"
```

Both have **map_public_ip_on_launch = true**, making them public.

---

## 3️⃣ Internet Gateway (IGW)

Allows resources inside the VPC to communicate with the internet.

```hcl
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.myvpc.id
}
```

---

## 4️⃣ Route Table + Associations

We create a route table with a default route to the IGW:

```hcl
route {
  cidr_block = "0.0.0.0/0"
  gateway_id = aws_internet_gateway.igw.id
}
```

Both subnets are associated with this table, enabling internet connectivity.

---

## 5️⃣ Security Group (Web-SG)

Allows:

* HTTP (80)
* SSH (22)
* All outbound traffic

Used by both **ALB** and **EC2** instances.

---

## 6️⃣ EC2 Instances (Web Servers)

Two EC2 instances:

* Placed in different public subnets
* Use user-data scripts to auto-install a web server
* Belong to the same security group

This ensures redundancy and load distribution.

---

## 7️⃣ Application Load Balancer (ALB)

The ALB distributes incoming traffic to both EC2 instances.

```hcl
resource "aws_lb" "myalb" {
  load_balancer_type = "application"
  internal = false
}
```

* Public-facing
* Uses the same security group
* Spans both subnets

---

## 8️⃣ Target Group

A target group that registers both EC2 instances.

Health checks ensure that only healthy servers receive traffic.

---

## 9️⃣ Target Group Attachments

Each EC2 instance is added to the target group.

---

## 🔟 Listener

A listener on port 80 forwards incoming traffic to the target group:

```hcl
default_action {
  type = "forward"
  target_group_arn = aws_lb_target_group.tg.arn
}
```

---

## 1️⃣1️⃣ S3 Bucket

An S3 bucket for storing assets or logs.

---

## 1️⃣2️⃣ Output (Load Balancer DNS)

Terraform prints the DNS of the ALB so you can access your web app:

```hcl
output "loadbalancerdns" {
  value = aws_lb.myalb.dns_name
}
```

---

# 🛠️ How to Use This Project

## ✔️ Step 1: Clone the Repository

```sh
git clone <your-repo-link>
cd <your-repo-folder>
```

## ✔️ Step 2: Initialize Terraform

```sh
terraform init
```

## ✔️ Step 3: Validate Terraform Files

```sh
terraform validate
```

## ✔️ Step 4: Apply and Create AWS Resources

```sh
terraform apply -auto-approve
```

## ✔️ Step 5: Get the Load Balancer URL

Terraform will output something like:

```
loadbalancerdns = myalb-1234567890.us-east-1.elb.amazonaws.com
```
(The url might differ in your case.)

Paste the URL into your browser to access the application.

---

# 🧹 Cleanup (Destroy Everything)

To delete all infrastructure created by Terraform:

```sh
terraform destroy -auto-approve
```

This removes:

* VPC
* Subnets
* EC2 instances
* ALB
* Security group
* S3 bucket
* IGW
* Route tables
* Target group
* Listener

---

# 🎯 Learning Outcomes

By completing this project, you learn:

* How to provision AWS resources using Terraform
* How VPC networking works
* How ALBs distribute traffic
* How to attach EC2 instances to a target group
* How to create fault-tolerant architectures
* How Terraform manages IaC (Infrastructure as Code)

---

# 🙌 Author

**Rehan Mohammed**
AWS | Terraform | Cloud Projects



