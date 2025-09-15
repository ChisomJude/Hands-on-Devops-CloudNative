
# 🧰 Setup Your Environment (Ubuntu VM)

This guide will walk you through setting up a reliable development and testing environment on an Ubuntu VM for our 3-month Cloud Native journey. This VM will host:
- [x]  Our FastAPI web application [See Project Repo](https://github.com/ChisomJude/student-project-tracker) ✅
- [x]  Dockerized services ✅
- [x]  Kubernetes cluster (Kind) ✅
- [x]  GitOps setup (ArgoCD) ✅
- [x]  Monitoring and observability stack (Prometheus, Grafana, Loki) ✅

---

## ☁️ Recommended VM Capacity

For a smooth experience, especially when running Kind clusters and monitoring tools:

| Resource     | Recommended Minimum |
|--------------|---------------------|
| **CPU**      | 2 vCPUs             |
| **RAM**      | 4 GB (8 GB ideal)   |
| **Storage**  | 30 GB SSD           |
| **OS**       | Ubuntu 20.04 or 22.04 LTS |

---

## 📦 Step-by-Step Setup Instructions

### 1. Update and Upgrade System
```bash
sudo apt update && sudo apt upgrade -y
```
### 2. Install Docker
```bash

sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```
***⚠️ Log out and back in for the Docker group to apply.***

### 3. Install Git
```bash
sudo apt install -y git
```

### 4.  Install Python 3 & Pip (for local testing)
```bash
sudo apt install -y python3 python3-pip
```

### 5.  Install kubectl
Use this guide for your kubectl and Kind as well as your cluster
  - https://k8s-kind-setup.hashnode.dev/title-getting-started-with-kubernetes-using-kind-kubernetes-in-docker


### 6. Install Helm (Package manager for K8s)

```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```


<img width="442" alt="image" src="https://github.com/user-attachments/assets/28d68a8f-daf6-4462-942c-a266416110bd" />

### 7. System Check 

```
kubectl cluster-info
docker --version
kubectl version --client
kind version
helm version
git --version
```

### Cluster Information



### 8.  Optional but helpful
- [x] Install Visual Studio Code on your Local PC (for Remote SSH extension)
- [x] Install tmux on your VM, for managing multiple sessions

```
sudo apt install -y tmux
```

#### Other useful commands with tmux
```
# start a session
tmux new -s mysession
#view all sessions
tmux ls
#attach a session
tmux attach -t mysession
```

## Joining After the Class, Please Check below to set up your DB properly


### Application repo: [Student Tracker App](https://github.com/ChisomJude/student-project-tracker)

### Database Url:  Database on Mongo , connect via Vault [Setup and Manage Vault ](https://github.com/ChisomJude/Manage-Hosted-Vault-Server/tree/master )

You can recreate your NO-SQL DB with [MongoDB - Atlas](https://account.mongodb.com/account/login?signedOut=true)


### Sample Data ;
``` {"_id":{"$oid":"68550a55a55e874c9322ccb6"},"id":"9dfb53c8-10e3-4f0e-9801-e4d8e188315a","name":"Maryb","progress":{"week1":"Completed","week2":"Completed","week3":"Completed","week4":"Completed","week6":"Completed","week5":"started"}}
```

![alt text](image.png)

