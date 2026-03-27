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
- 
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
<img width="1648" height="781" alt="Screenshot 2026-03-27 163306" src="https://github.com/user-attachments/assets/0e2b9f6b-83b9-48b0-b9b0-24148a2f913c" />

<img width="1744" height="877" alt="Screenshot 2026-03-27 163401" src="https://github.com/user-attachments/assets/c1fedf46-22a5-47a0-933e-5849ad6fe546" />

I did exactly the same configuration for all three instances.

<img width="1895" height="581" alt="Screenshot 2026-03-27 163601" src="https://github.com/user-attachments/assets/8157a5b2-33dd-4280-a68c-a4bbd80240e4" />

---

## Step 3: Elastic IP Configuration
Note the Private and Public IPs

| Hostname | Private IP | Public IP |
|----------|------------|-----------|
| k3s-master-1 | 172.31.83.8 | 54.166.216.17 |
| k3s-master-2 | 172.31.88.163 | 54.152.187.63 |
| k3s-master-3 | 172.31.83.62 | 54.86.242.80 |

I noticed that whenever I stopped and restarted my lab, the public IP addresses changed.  
To fix this, I created an Elastic IP.

I assigned the Elastic IP only to the **First Master Node** because it acts as the main cluster endpoint.

<img width="1901" height="905" alt="Screenshot 2026-03-27 135619" src="https://github.com/user-attachments/assets/417b005e-1334-4134-bcdf-3a2070ae5ae1" />
<img width="1779" height="806" alt="Screenshot 2026-03-27 135806" src="https://github.com/user-attachments/assets/059ed6fa-3820-4bef-bc12-e7d30f754f4a" />
<img width="1906" height="764" alt="Screenshot 2026-03-27 140017" src="https://github.com/user-attachments/assets/e361492c-252a-4311-bf0c-04c59f3f6297" />
<img width="1856" height="693" alt="Screenshot 2026-03-27 140208" src="https://github.com/user-attachments/assets/d1af9c10-e2e1-4bd5-a313-3f5a11a28afe" />
<img width="1908" height="829" alt="Screenshot 2026-03-27 140307" src="https://github.com/user-attachments/assets/533cfacd-faeb-4a1a-9230-90ba532c86f1" />


---

## Step 4: Prepare Nodes
I prepared all nodes by:
- Setting hostnames
- Updating packages
- Configuring `/etc/hosts`
- Disabling swap

<img width="1165" height="930" alt="Screenshot 2026-03-21 000116" src="https://github.com/user-attachments/assets/7d76a9a8-a796-4f95-8649-6adebf22add7" />

---

## Step 5: Install K3s on First Master Node
I created the K3s configuration file and installed K3s on the First Master Node.  
Then I verified the installation using kubectl commands and retrieved the cluster join token.

<img width="1085" height="1006" alt="Screenshot 2026-03-27 141428" src="https://github.com/user-attachments/assets/30d88938-91f5-4de3-83b0-ccff67fa18cf" />

<img width="1246" height="938" alt="Screenshot 2026-03-27 141603" src="https://github.com/user-attachments/assets/701d8066-48f9-4320-830e-46541d2c7fab" />

<img width="1026" height="305" alt="Screenshot 2026-03-27 143804" src="https://github.com/user-attachments/assets/d1805090-029c-4101-aa23-b3c66dd1e6b4" />

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
<img width="1038" height="566" alt="Screenshot 2026-03-27 150557" src="https://github.com/user-attachments/assets/99f9a718-385e-4902-86c1-b221f3f35d7d" />

---

## Step 8: Fix (Security Group)
To resolve the issue, I created a new security group called **k3s-cluster-sg** and allowed the required ports, including port 6443.

I then assigned this security group to all three instances so they could communicate with each other.

<img width="1907" height="780" alt="Screenshot 2026-03-27 151928" src="https://github.com/user-attachments/assets/322b4f47-7181-4fdd-a368-7e004f8bea79" />
<img width="1742" height="765" alt="Screenshot 2026-03-27 153305" src="https://github.com/user-attachments/assets/bfa2e026-c906-4f60-a2a3-67a09ed9eeb6" />
<img width="1609" height="530" alt="Screenshot 2026-03-27 154552" src="https://github.com/user-attachments/assets/7da24f59-e21f-4724-81d8-813f98168370" />
<img width="1623" height="308" alt="Screenshot 2026-03-27 155612" src="https://github.com/user-attachments/assets/2c870699-f170-43da-a62c-4e52e76cd55f" />
<img width="1659" height="434" alt="Screenshot 2026-03-27 155935" src="https://github.com/user-attachments/assets/6578e2a3-7193-42a3-a838-4bc81441acad" />
<img width="1741" height="638" alt="Screenshot 2026-03-27 160108" src="https://github.com/user-attachments/assets/e9ffd916-5c5c-4128-b86c-57f6ebc869e2" />


---

## Step 9: Connectivity Test
I tested connectivity using curl:
```bash
curl -k https://172.31.83.8:6443
```
I received a 401 Unauthorized response, which confirmed that the API server was reachable.

---

## Step 10: Cluster Verification

After fixing the issue, I restarted K3s on nodes 2 and 3 and confirmed that all nodes joined successfully.

<img width="1908" height="819" alt="Screenshot 2026-03-27 190719" src="https://github.com/user-attachments/assets/a18dd32f-7cef-48e9-aece-4b5d85ad25b3" />
<img width="1906" height="699" alt="Screenshot 2026-03-27 160842" src="https://github.com/user-attachments/assets/c756d5bb-698a-4eae-8196-4d450ef85dda" />

---

## Step 11: Pods Verification

I verified that all system pods were running correctly.

<img width="1580" height="542" alt="Screenshot 2026-03-27 161902" src="https://github.com/user-attachments/assets/8059bff3-3284-424b-816f-72c6f9049af9" />
<img width="1908" height="819" alt="Screenshot 2026-03-27 190719" src="https://github.com/user-attachments/assets/098483fa-59b2-43db-96d2-e05e9919156c" />

---

## Reflection

This assignment helped me understand how to deploy a K3s cluster on AWS and how cloud networking affects Kubernetes communication. I learned how to configure EC2 instances, assign Elastic IPs, and prepare Linux systems for cluster deployment.

One challenge I faced was that the nodes could not join the cluster due to a security group issue. I fixed this by creating a new security group and allowing the correct ports. This helped me understand the importance of networking in cloud environments.

I also learned how K3s simplifies Kubernetes while still using the same core concepts such as control planes, nodes, and cluster communication. This experience improved my skills in troubleshooting, cloud setup, and Kubernetes deployment.

---

## Conclusion

This project successfully demonstrated the deployment of a K3s cluster using three master nodes on AWS EC2. It included infrastructure setup, cluster configuration, troubleshooting, and verification of a working system.



