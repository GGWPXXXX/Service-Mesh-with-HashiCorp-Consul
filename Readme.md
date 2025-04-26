### Demo project accompanying a [Consul crash course video](https://www.youtube.com/watch?v=s3I1kKKfjtQ) on YouTube

---

## Todo - Setup Instructions

1. **Clone the project**

```
git clone https://github.com/GGWPXXXX/Service-Mesh-with-HashiCorp-Consul
```

(this repo was forked manually from GitLab)

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

---

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
