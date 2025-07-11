
## ✅ Objective

- Provision an EKS cluster using Terraform  
- Deploy NGINX using Kubernetes manifests  
- Setup ArgoCD for GitOps and sync from GitHub  
- Access the NGINX app via port-forwarding  

---

## 🧰 Tools & Technologies

- AWS (EKS, IAM, VPC)  
- Terraform  
- kubectl  
- Docker  
- ArgoCD  
- GitHub  

---

## 💻 EC2 Instance Setup

### 🔹 Create EC2 (Ubuntu 22.04, t3.medium, 20GB gp3)

### 🔹 Security Group Inbound Rules:
- SSH (22) from My IP  
- HTTP (80) from Anywhere  
- HTTPS (443) from Anywhere  
- Custom TCP (8080, 8081) from Anywhere  

### 🔹 Connect to EC2:
```bash
chmod 400 your-key.pem
ssh -i your-key.pem ubuntu@18.227.161.253
```

### 🔹 Initial Folder Setup:
```bash
mkdir devops-pipeline && cd devops-pipeline
mkdir terraform argocd manifests
```

---

## 🛠️ Terraform – Provision EKS Cluster

### 🔹 Create main.tf
```bash
vim terraform/main.tf
# (Paste full EKS provisioning code here)
```

### 🔹 Create variables.tf
```bash
vim terraform/variables.tf
```

### 🔹 Create outputs.tf
```bash
vim terraform/outputs.tf
```

### 🔹 Terraform Commands (One-liner):
```bash
cd terraform && terraform init && terraform plan && terraform apply -auto-approve
```

---

## 🧪 kubectl – Interact with Kubernetes

### 🔹 Configure kubeconfig:
```bash
aws eks update-kubeconfig --region us-east-1 --name devops-cluster
```

### 🔹 Deploy NGINX:
```bash
kubectl apply -f manifests/nginx-deployment.yaml
kubectl apply -f manifests/nginx-service.yaml
```

### 🔹 Port-forward NGINX:
```bash
kubectl port-forward svc/nginx-service 8081:80 --address=0.0.0.0
```

---

## 🐳 Docker (Optional)
```bash
sudo apt update && sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
```

---

## 🔄 ArgoCD – GitOps CD

### 🔹 Install ArgoCD:
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl wait --for=condition=available --timeout=300s deployment/argocd-server -n argocd
```

### 🔹 Access ArgoCD UI:
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address=0.0.0.0
```

Open in browser: https://18.227.161.253:8080

### 🔹 Get admin password:
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### 🔹 Apply ArgoCD App:
```bash
kubectl apply -f argocd/nginx-app.yaml
```

---

## 🗃️ GitHub – Store Code and Deploy via ArgoCD

### 🔹 Git Commands:
```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/Mkudte09/devops-pipeline.git
git push -u origin main
```

---

## 🧹 Cleanup Commands
```bash
kubectl delete -f argocd/nginx-app.yaml
kubectl delete namespace argocd
cd terraform && terraform destroy
```
