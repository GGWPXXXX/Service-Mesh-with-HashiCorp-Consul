### Demo project accompanying a [Consul crash course video](https://www.youtube.com/watch?v=s3I1kKKfjtQ) on YouTube

---

## Todo - Setup Instructions

1. **Clone the project**

   ```
   git clone https://github.com/GGWPXXXX/Service-Mesh-with-HashiCorp-Consul
   ```

(this repo was forked manually from [GitLab](https://gitlab.com/twn-youtube/consul-crash-course) )

2. **Create a `terraform.tfvars` file** in the terraform directory.  
   Add your AWS credentials like this:

   ```hcl
   aws_access_key_id     = "YOUR_AWS_ACCESS_KEY_ID"
   aws_secret_access_key = "YOUR_AWS_SECRET_ACCESS_KEY"
   ```

👉 **Note:**  
You need AWS credentials with permissions to:

- Manage EC2 (VPCs, subnets, security groups)
- Manage EKS (clusters and nodegroups)
- Manage IAM (roles and policies)
- Manage CloudWatch Logs

If unsure, attach the **AdministratorAccess** policy to your IAM user.

## Create K8S Cluster on AWS EKS

1. **Make sure you in the terraform directory.**

2. **Initialize terraform**

   ```
   terraform init
   ```

3. **Create the cluster**

   ```
   terraform apply -var-file terraform.tfvars
   ```

   This will create a new EKS cluster with 3 nodes in the `ap-southeast-1` region.
   You can change the region in the `variables.tf` file.

4. **Configure AWS CLI**

   Make sure you have the AWS CLI installed and configured with your credentials.

   ```
   aws configure
   ```

5. **Create kubeconfig file for cluster in ~/.kube**

   ```
   aws eks update-kubeconfig --region ap-southeast-1 --name myapp-eks-cluster
   ```

6. **Check connection to the cluster**

   ```
   kubectl get node
   ```

   **You should see 3 working nodes.**

---

## Deploy Microservices application into K8S cluster

1. **Make sure you in the kubernetes directory.**
2. **Apply kubernetes config file.**

   ```
   kubectl apply -f config.yaml
   ```

3. **Check status of the pods**

   ```
   kubectl get pod
   ```

## Deployment Consul on EKS

1. **Make sure you in the kubernetes directory.**

2. **Install Helm CLI**

   ```
   choco install kubernetes-helm
   ```

   (or use the [Helm installation guide](https://helm.sh/docs/intro/install/) for your OS)
   Make sure you have the latest version of Helm installed.

3. **Add & Install Consul**
   ```
   helm repo add hashicorp https://helm.releases.hashicorp.com
   helm install eks hashicorp/consul --version 1.0.0 --values consul-values.yaml --set global.datacenter=eks
   ```
4. **Check for the consul status in the pod**
   ```
   kubectl get pod
   ```
   You should see eks-consul-\* up and running.

## Terraform commands to execute the script

```sh
# initialise project & download providers
terraform init

# preview what will be created with apply & see if any errors
terraform plan

# exeucute with preview
terraform apply -var-file terraform.tfvars

# execute without preview
terraform apply -var-file terraform.tfvars -auto-approve

# destroy everything
terraform destroy

# show resources and components from current state
terraform state list
```

## Get access to EKS cluster

```sh
# install and configure awscli with access creds
aws configure

# check existing clusters list
aws eks list-clusters --region eu-central-1 --output table --query 'clusters'

# check config of specific cluster - VPC config shows whether public access enabled on cluster API endpoint
aws eks describe-cluster --region eu-central-1 --name myapp-eks-cluster --query 'cluster.resourcesVpcConfig'

# create kubeconfig file for cluster in ~/.kube
aws eks update-kubeconfig --region eu-central-1 --name myapp-eks-cluster

# test configuration
kubectl get svc
```
