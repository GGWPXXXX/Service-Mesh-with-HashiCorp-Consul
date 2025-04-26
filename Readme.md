# Consul Crash Course - Demo Project Setup

(Based on [Consul Crash Course Video](https://www.youtube.com/watch?v=s3I1kKKfjtQ))

---

## 📚 Project Overview

This project accompanies a crash course on using HashiCorp Consul as a service mesh across Kubernetes clusters on AWS (EKS) and Azure (AKS).

---

## ⚙️ Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/GGWPXXXX/Service-Mesh-with-HashiCorp-Consul
```

(This repo was forked manually from [GitLab Source](https://gitlab.com/twn-youtube/consul-crash-course))

---

### 2. Create a `terraform.tfvars` File

Inside the `terraform/` directory, create a file named `terraform.tfvars` with your AWS credentials:

```hcl
aws_access_key_id     = "YOUR_AWS_ACCESS_KEY_ID"
aws_secret_access_key = "YOUR_AWS_SECRET_ACCESS_KEY"
```

**Required Permissions**:

- EC2 (VPCs, Subnets, Security Groups)
- EKS (Clusters, Node Groups)
- IAM (Roles, Policies)
- CloudWatch Logs

_Tip: Attach the `AdministratorAccess` policy if unsure._

---

## ☁️ Deploy Kubernetes Cluster - AWS EKS

### Step 1: Initialize Terraform

```bash
cd terraform
terraform init
```

### Step 2: Apply Terraform Configuration

```bash
terraform apply -var-file terraform.tfvars
```

(Creates an EKS cluster with 3 nodes in `ap-southeast-1` — region configurable in `variables.tf`.)

### Step 3: Configure AWS CLI

```bash
aws configure
```

### Step 4: Generate Kubeconfig

```bash
aws eks update-kubeconfig --region ap-southeast-1 --name myapp-eks-cluster
```

### Step 5: Verify Cluster Access

```bash
kubectl get nodes
```

_You should see 3 nodes ready._

---

## 🚀 Deploy Microservices Application

1. Change directory to `kubernetes/`
2. Apply configuration:

```bash
kubectl apply -f config.yaml
```

3. Verify pods:

```bash
kubectl get pods
```

---

## 🔥 Deploy Consul on EKS

### Step 1: Install Helm

```bash
choco install kubernetes-helm
```

(Or follow the [Helm Install Guide](https://helm.sh/docs/intro/install/))

### Step 2: Install Consul

```bash
helm repo add hashicorp https://helm.releases.hashicorp.com
helm install eks hashicorp/consul --version 1.0.0 --values consul-values.yaml --set global.datacenter=eks
```

### Step 3: Check Pod Status

```bash
kubectl get pods
```

You should see `eks-consul-*` pods running.

---

## ☁️ Deploy Kubernetes Cluster - Azure AKS

### Step 1: Connect to AKS Cluster

```bash
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

(This merges AKS kubeconfig into your `~/.kube/config`.)

### Step 2: Verify Cluster Access

```bash
kubectl get nodes
```

### Step 3: Install Consul on AKS

```bash
helm install aks hashicorp/consul --version 1.0.0 --values consul-values.yaml --set global.datacenter=aks
```

### Step 4: Check Pod Status

```bash
kubectl get pods
```

---

## 🌉 Connect the Two Clusters (EKS and AKS)

1. Run in each cluster:

```bash
kubectl apply -f consul-mesh-gateway.yaml
```

2. Switch context between clusters and repeat.

3. Connect via `*-consul-ui` External IPs.

---

## 🧹 Destroy AWS Resources

When finished, destroy the EKS cluster:

```bash
cd terraform
terraform destroy
```

---

# 🎯 Final Notes

- Always double-check kubeconfig context when working across clusters.
- This project demonstrates multi-cloud service mesh setup with Consul, Helm, Terraform, and Kubernetes.
