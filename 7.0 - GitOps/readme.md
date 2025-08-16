# GITOPS  - with Gitactions and Argocd

## Set up your environment
On a new EC2 instance, run these installations (skip if already set up). Ensure all the ports you intend to use are enabled on your security group - 80,8080,8000 etc

```
# Update base
sudo apt-get update -y

# Install Docker
sudo apt-get install -y ca-certificates curl gnupg lsb-release
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update -y
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker $USER
newgrp docker

# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl && sudo mv kubectl /usr/local/bin/

# Install kind
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
chmod +x kind && sudo mv kind /usr/local/bin/

# Install Helm
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash


```

## Create your kind cluster 

```
cat <<EOF > kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
  - containerPort: 443
    hostPort: 443
- role: worker
EOF

kind create cluster --config kind-config.yaml --name kind

# Confirm using kubectl get nodes

```


## Install ArgoCD
We’re not using ingress here; we’ll expose Argo CD on :8080 and the app on :8000 via port-forward, to use ingress, follow this guide here for your cluster setup - https://github.com/kubernetes-sigs/cloud-provider-kind

```
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

# Mrun server in --insecure (HTTP) because we’ll port-forward over 8080 .
cat > argo-values.yaml <<'EOF'
server:
  extraArgs:
    - --insecure
  service:
    type: ClusterIP
EOF

# install argocd
kubectl create namespace argocd 2>/dev/null || true
helm upgrade --install argo-cd argo/argo-cd -n argocd -f argo-values.yaml

# Wait for pods and confirm services 
kubectl -n argocd get pods,svc

# Initial admin password - user: admin ( you can change the generated password on the UI or Save the initial )
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo


# Expose the UI on all interfaces (EC2 public IP:8080) 
kubectl -n argocd port-forward svc/argo-cd-argocd-server 8080:80 --address 0.0.0.0

# or Use nohup to run in background mode
nohup kubectl -n argocd port-forward svc/argo-cd-argocd-server 8080:80 --address 0.0.0.0 > argocd-portforward.log 2>&1 &

# tail your Argocd logs on 
tail -f argocd-portforward.log

#to kill the process for argocd , get the PID and Kill
ps aux | grep 'kubectl -n argocd port-forward'
kill -9 <pid>   #eg kill -9 230745

```
#### Remember to update your initial Password in User Info 

<img width="1194" height="280" alt="image" src="https://github.com/user-attachments/assets/5a3073c2-5566-4174-bcb2-512fcab09646" />

<img width="1355" height="604" alt="image" src="https://github.com/user-attachments/assets/5d8b80e8-8cf9-4b12-b624-21d2c6094a97" />


## Application Deployment 

Let's prepare our app repo using the format [Helm App Repo ](https://github.com/ChisomJude/student-progress-tracker2) 
Free free to reuse Week 6 repo since you already have a working CI or recreate as shown in the link above



