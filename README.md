# assignment-1-mbirize-mboni
# Assignment 1 — K3s on AWS EC2

## Student Information
- Full Name: Mbirize Victoire Mboni
- Student Number: 221477926

## Purpose
This project demonstrates the deployment of a lightweight Kubernetes cluster using K3s on AWS EC2. The goal was to provision infrastructure, configure networking, install K3s, and verify a working high-availability cluster.

## System Requirements
- Cloud Platform: AWS EC2
- Number of Instances: 3
- Instance Type: t3.large
- CPU: 2 vCPU
- RAM: 8 GB
- Disk: 50 GB per instance
- OS: Ubuntu 24.04 LTS
- Security Group: k3s-cluster-sg

---

## Architecture Explanation
This setup consists of three EC2 instances acting as master nodes in a K3s high-availability cluster.

- First Master Node initializes the cluster
- Master Nodes 2 and 3 join using a token
- Internal communication uses private IPs
- Elastic IP is assigned only to the First Master Node
- Security group allows cluster communication

---

## Step 1: EC2 Instance Creation
I created three EC2 instances using Ubuntu 24.04 and configured them for the K3s cluster.

<img width="1745" height="769" alt="Screenshot 2026-03-27 163158" src="https://github.com/user-attachments/assets/599d9af0-8de3-47ce-a301-4723f2720eda" />


---

## Step 2: Instance Configuration
I configured all three instances with:
- t3.large instance type
- SSH access
- 50 GB storage per instance

I did exactly the same configuration for all three instances.

---

## Step 3: Elastic IP Configuration
I noticed that whenever I stopped and restarted my lab, the public IP addresses changed.  
To fix this, I created an Elastic IP.

I assigned the Elastic IP only to the **First Master Node** because it acts as the main cluster endpoint.

![Elastic IP](images/elastic-ip.png)

---

## Step 4: Prepare Nodes
I prepared all nodes by:
- Setting hostnames
- Updating packages
- Configuring `/etc/hosts`
- Disabling swap

---

## Step 5: Install K3s on First Master Node
I created the K3s configuration file and installed K3s on the First Master Node.  
Then I verified the installation using kubectl commands and retrieved the cluster join token.

![K3s Installation](images/k3s-install.png)

---

## Step 6: Join Master Nodes 2 and 3
I configured both nodes with:
- server URL (First Master Node)
- token from master node
- their private IPs

Then I installed K3s as server nodes.

---

## Step 7: Error Encountered
While joining nodes 2 and 3, the installation failed because the nodes could not communicate with the First Master Node on port 6443.

---

## Step 8: Fix (Security Group)
To resolve the issue, I created a new security group called **k3s-cluster-sg** and allowed the required ports, including port 6443.

I then assigned this security group to all three instances so they could communicate with each other.

![Security Group](images/security-group.png)

---

## Step 9: Connectivity Test
I tested connectivity using curl:

```bash
curl -k https://172.31.83.8:6443
