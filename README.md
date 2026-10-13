# EKS Foundations with Terraform

A Terraform project for building an Amazon EKS cluster, with a pinned toolchain managed by mise and state stored in S3.

> Based on [lm-academy/eks-foundations-starter](https://github.com/lm-academy/eks-foundations-starter), part of the student material for the Amazon EKS with Terraform course.

## Project layout

- `infra/`: the Terraform project (`versions.tf`, `providers.tf`, `backend.tf`), with state in an S3 backend.
- `k8s/`: Kubernetes manifests (an nginx Deployment and a LoadBalancer Service).
- `_labs/`: notes for each step of the build, each with its goal, the configuration to target, and the tasks.
- `mise.toml`: pinned tool versions and the `AWS_REGION` environment variable.
- `.devcontainer/`: an alternative devcontainer with the same tools preinstalled.
- `.gitattributes`: enforces LF line endings.
- `.gitignore`: keeps Terraform state, plans, `.tfvars`, and local kubeconfigs out of version control.

## Set up the toolchain

Tools are pinned in `mise.toml`: Terraform `1.15.8`, AWS CLI `2.36.4`, kubectl `1.36.2`. mise also sets `AWS_REGION=us-east-1` whenever you are inside the project directory.

```bash
mise trust
mise install
```

Confirm the versions:

```bash
terraform version            # Terraform v1.15.8
aws --version                # aws-cli/2.36.4
kubectl version --client     # Client Version: v1.36.2
```

## Configure AWS access

Sign in with the AWS CLI using your console credentials, then confirm which identity you are authenticated as:

```bash
aws login
aws sts get-caller-identity
```

To work in a different region, change `AWS_REGION` in `mise.toml`.

## Initialize Terraform

State is stored in the `tf-state-fugalkev-nonprod` S3 bucket under the key `eks-foundations/terraform.tfstate`, with native S3 state locking (`use_lockfile`). The bucket is set in `infra/backend.tf` and the region is read from `AWS_REGION`, so no extra flags are needed:

```bash
cd infra
terraform init
```

Expect `Successfully configured the backend "s3"!` and `Terraform has been successfully initialized!`.

## Costs

The cluster, the NAT gateway, the nodes, and the load balancer are all billed resources. Tear everything down at the end of a session:

```bash
terraform destroy
```
