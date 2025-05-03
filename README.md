# MERN Stack Deployment on AWS using Terraform and Ansible

## Project Overview

This project showcases the automated provisioning and configuration of a three-tier MERN (MongoDB, Express.js, React, Node.js) stack application using **Terraform** and **Ansible** on **AWS**. It follows best practices for infrastructure as code, configuration management, and secure deployment.

---

## 📁 Project Structure

```bash
mern-aws-terraform-ansible/
│
├── Terraform/                      # Infrastructure provisioning with Terraform
│   ├── ec2/                        # EC2 instances module
│   ├── vpc/                        # VPC and networking setup
│   ├── security/                   # Security groups and firewall rules
│   ├── main.tf                     # Root Terraform file integrating modules
│   ├── variables.tf                # Input variables
│   ├── outputs.tf                  # Output definitions (e.g., IP addresses)
│   ├── provider.tf                 # AWS provider setup
│   ├── terraform.tfvars            # Actual values for variables
│   └── scripts/                    # Optional shell scripts if required
│
├── ansible/                        # Ansible configuration and automation
│   ├── inventory.ini               # Hosts and groups with IPs and SSH details
│   └── playbooks/
│       ├── db_setup.yml            # MongoDB installation and security configuration
│       ├── backend_setup.yml       # Backend deployment (Node.js/Express)
│       └── frontend_setup.yml      # Frontend deployment (React)
│
├── screenshots/                    # Screenshots of working deployment (optional)
│
└── README.md                       # Documentation and instructions
```

---

## ✅ Prerequisites

* AWS account with permissions for EC2, VPC, IAM, etc.
* SSH key pair for EC2 instance access
* [Terraform](https://www.terraform.io/downloads.html) installed
* [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html) installed
* AWS CLI installed and configured (`aws configure`)

---

## 🚀 Step 1: Infrastructure Setup Using Terraform

### 1️⃣ Initialize and Apply Terraform

```bash
cd Terraform
terraform init
terraform apply -auto-approve
```

This will provision:

* Public EC2 instance for frontend/backend
* Private EC2 instance for MongoDB
* Security groups and networking components

### 2️⃣ Capture Outputs

Terraform outputs include:

* `web_public_ip` — Public IP of frontend/backend instance
* `db_private_ip` — Private IP of MongoDB instance

### 3️⃣ Cleanup Resources

To destroy all AWS infrastructure:

```bash
cd Terraform
terraform destroy -auto-approve
```

---

## 🔐 AWS Credentials Configuration

Ensure that the AWS CLI is configured with access credentials:

```bash
aws configure
```

Set up the following:

* AWS Access Key ID
* AWS Secret Access Key
* Region (e.g., `us-west-1`)

---

## ⚙️ Step 2: Configuration and Deployment with Ansible

### 1️⃣ Ansible Inventory

Update the `ansible/inventory.ini` with IPs from Terraform:

```ini
[frontend]
<frontend_public_ip> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/your-key.pem

[backend]
<frontend_public_ip> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/your-key.pem

[database]
<db_private_ip> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/your-key.pem
```

### 2️⃣ Environment Variables & App Configuration

* Frontend: `.env` contains `REACT_APP_BACKEND_URL=http://<public_ip>:3001`
* Backend: `MONGO_URL=mongodb://<private_ip>:27017/travelmemory`

These variables are set inside playbooks before application deployment.

### 3️⃣ Run Ansible Playbooks

```bash
cd ansible
ansible-playbook -i inventory.ini playbooks/db_setup.yml
ansible-playbook -i inventory.ini playbooks/backend_setup.yml
ansible-playbook -i inventory.ini playbooks/frontend_setup.yml
```

Each playbook will:

* Provision required packages
* Clone GitHub repos
* Configure services
* Export environment variables
* Start the application

---

## 🧱 Application Architecture

* **Frontend** (React): Served via `serve` package on port `3000`
* **Backend** (Express.js): Running on Node.js, listening on port `3001`
* **Database** (MongoDB): Privately hosted on EC2, exposed only to backend

```
[Client] ---> [React Frontend (Port 3000)] ---> [Express Backend (Port 3001)] ---> [MongoDB (Port 27017)]
```

---

## 🔐 Security Best Practices

* Allow only required inbound ports (22, 3000, 3001)
* Use private subnets for sensitive services (e.g., MongoDB)
* SSH with private key authentication only
* Disable root login and unnecessary services

---

## 🛠️ Troubleshooting

* **SSH Timeout**: Ensure correct key and open port 22 in SG
* **App Crashes**: Check logs via Ansible output or SSH into instance
* **DB Connectivity**: Confirm MongoDB IP/port accessible from backend instance

---

## 📷 Output & Demonstration

* Frontend accessible at: `http://<frontend_public_ip>:3000`
* Backend API at: `http://<frontend_public_ip>:3001`
* MongoDB: accessible internally only

---

## 👩‍💻 Author

**Reshma Navale**

Feel free to reach out for queries or collaboration.
