# Assignment 5 — Deploy Book Review App in Your Favorite Cloud (Agentic Terraform Project)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

This is the most important assignment of the Terraform section. You will deploy the Book Review App in a production-style three-tier architecture using Terraform on your choice of AWS or Azure — six subnets across two Availability Zones, tier-specific security rules, public and internal load balancers, Next.js/Node.js on Ubuntu VMs, and a private managed MySQL database with a read replica. This assignment is agent-assisted: you may use Claude Code, ChatGPT, or another LLM tool to help design, generate, debug, and improve the infrastructure.

---

# Task 1 — VPC/VNet and Subnet Setup

## Goal

Create a custom VPC/VNet (10.0.0.0/16) with six subnets across two Availability Zones: two public Web Tier subnets, two private App Tier subnets, and two private Database Tier subnets, implemented with Terraform.

### Evidence

#### Screenshot 1 — VPC or VNet details showing 10.0.0.0/16

![alt text](image-40.png)

---

#### Screenshot 2 — Subnet list showing all six subnets, their tiers, CIDR ranges, and Availability Zones

![alt text](image-41.png)

---

#### Screenshot 3 — Terraform plan or cloud networking view showing the required routing and tier isolation

![alt text](image-42.png)

---

# Task 2 — Security Groups/NSGs and Load Balancers

## Goal

Configure tier-specific Security Groups/NSGs (Web Tier HTTP 80, App Tier 3001 only from Web Tier, Database Tier 3306 only from App Tier), and create a public load balancer for the frontend and an internal load balancer for the backend, all with Terraform.

### Evidence

#### Screenshot 4 — Web, App, and Database Security Group or NSG rules

![alt text](image-43.png)

---

#### Screenshot 5 — Public frontend load balancer configuration

![alt text](image-68.png)

---

#### Screenshot 6 — Internal backend load balancer configuration

![alt text](image-44.png)

---

#### Screenshot 7 — Healthy frontend and backend targets or backend pools

![alt text](image-45.png)

---

# Task 3 — VMs and Application Deployment

## Goal

Deploy the Next.js Web Tier behind Nginx on port 80 in the public subnets, and the Node.js App Tier on port 3001 in the private subnets (no Elastic IPs/Public IPs on private VMs), with the frontend reaching the backend through the internal load balancer.

### Evidence

#### Screenshot 8 — EC2 or Azure VM dashboard showing the frontend and backend VMs

![alt text](image-46.png)

---

#### Screenshot 9 — Nginx status or frontend response on the Web Tier

![alt text](image-65.png)

---

#### Screenshot 10 — Backend API response through the permitted internal path

![alt text](image-67.png)

---

# Task 4 — MySQL Database Setup

## Goal

Deploy a private managed MySQL database (Amazon RDS Multi-AZ or Azure Database for MySQL Flexible Server) with a read replica, restricted to the App Tier on port 3306, and validate the Book Review App homepage, login, review flow, backend API, and database integration through the public load balancer.

### Evidence

#### Screenshot 11 — Amazon RDS or Azure Database dashboard showing the primary database and read replica

![alt text](image-47.png)

---

#### Screenshot 12 — Evidence of private database networking and permitted App Tier access

![alt text](image-48.png)

---

#### Screenshot 13 — Functional Book Review App homepage and login flow

![alt text](image-69.png)

---

#### Screenshot 14 — Functional review flow with working backend API and database integration

![alt text](image-70.png)

---

#### Screenshot 15 (optional) — Application logs or terminal output



---

### Notes

Report the cloud platform used (AWS or Azure), your Terraform code structure (`main.tf`, `variables.tf`, `outputs.tf`, and supporting files), a link/description of your architecture diagram, and the Public Load Balancer DNS used to access the frontend.

### Cloud Platform

**Azure** was used as the cloud platform for this project. The infrastructure was provisioned using **Terraform** in the **South Africa North** Azure region.

### Terraform Code Structure

The Terraform configuration is organized into a root configuration with reusable modules:

* **`main.tf`** — Defines and connects the major infrastructure modules and resources.
* **`variables.tf`** — Defines configurable inputs such as the resource group, Azure region, VNet address space, subnet CIDRs, and administrative source IP.
* **`outputs.tf`** — Exposes useful deployment information such as resource identifiers and frontend access information.
* **`terraform.tfvars`** — Supplies project-specific values without hard-coding them into the main configuration.
* **`providers.tf`** — Configures the AzureRM Terraform provider.
* **`modules/network/`** — VNet and subnet configuration.
* **`modules/security/`** — Network Security Groups and security rules.
* **`modules/web_compute/`** — Private web-tier virtual machines and networking.
* **`modules/app_compute/`** — Private application-tier virtual machines and networking.
* **`modules/internal_load_balancer/`** — Internal load balancing for the application tier.
* **`modules/public_load_balancer/`** — Public Application Gateway/load-balancing layer for frontend access.
* **`modules/database/`** — Private Azure Database for MySQL Flexible Server, database, private DNS, and read replica.

The architecture follows a three-tier design: **public frontend/load-balancing → private web tier → private application tier → private database tier**.

### Architecture Diagram

The architecture diagram illustrates the flow:

**Internet → Public Application Gateway → Private Web VMs → Internal Load Balancer → Private App VMs → Private MySQL Database**

The diagram also shows the VNet, subnet separation, Network Security Groups, private DNS, and the two web/application instances.

### Public Load Balancer DNS

The frontend is accessed through the **public Application Gateway**. The public endpoint/DNS used for the application should be taken from the Terraform output or Azure portal for the deployed Application Gateway.

**Public front**


---

# LinkedIn Post (Required)

## Goal

Publish a LinkedIn post about what you achieved in this assignment, with public or "Anyone" visibility.

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/adepoju-adekunle-43217aa4_dmi-devops-micro-internship-with-agentic-activity-7499545460890656776-GnO6?utm_source=share&utm_medium=member_desktop&rcm=ACoAABYYCOYB1CQ-AKDgCJ7ecCiAgMVI9f2fFws

---

#### Screenshot 16 — Published LinkedIn post showing the text and at least one image or proof

![alt text](image-39.png)

---

# Submission Instructions

- Add all required screenshots in your submission
- Include your architecture diagram and Public Load Balancer DNS
- Do not expose passwords, keys, tokens, database credentials, or Terraform state secrets

---

# Completion Checklist

- [ ] Task 1: Six-subnet VPC/VNet created across two AZs with Terraform (Screenshots 1–3)
- [ ] Task 2: Tier-specific security rules and load balancers configured (Screenshots 4–7)
- [ ] Task 3: Web and App Tier VMs deployed with correct public/private placement (Screenshots 8–10)
- [ ] Task 4: Private MySQL with read replica deployed and app validated end to end (Screenshots 11–15)
- [ ] Report completed: cloud platform, Terraform structure, diagram, LB DNS (Notes)
- [ ] LinkedIn post published and URL submitted (Screenshot 16)
- [ ] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
