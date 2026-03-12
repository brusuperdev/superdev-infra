# EKS Cluster Setup

## Cluster Details

| Item | Value |
|---|---|
| Cluster Name | `bruviti-ai-superdev-eks` |
| Region | `us-east-1` |
| Kubernetes Version | 1.31 |
| VPC | `vpc-0a05f0eb7f3518554` |
| Node Group | `superdev-ng-1` (t3.medium, 1 node, max 3) |
| Namespace | `superdev` |
| Config | `eks/cluster-config.yaml` |

## Subnets

| Name | Subnet ID | AZ | CIDR | Type |
|---|---|---|---|---|
| Public Subnet 1 | `subnet-04e578655174608e0` | us-east-1a | 10.7.0.0/26 | Public (auto-assign IP enabled) |
| Public Subnet 2 | `subnet-0fa1ad0795e421352` | us-east-1b | 10.7.0.64/26 | Public (auto-assign IP enabled) |
| Private Subnet 1 | `subnet-0170ff2c675f7d3d2` | us-east-1a | 10.7.0.128/26 | Private |
| Private Subnet 2 | `subnet-0b2cb16c0f29bc30e` | us-east-1b | 10.7.0.192/26 | Private |

## CloudFormation Stacks (created by eksctl)

| Stack | Purpose |
|---|---|
| `eksctl-bruviti-ai-superdev-eks-cluster` | EKS control plane, IAM roles, security groups |
| `eksctl-bruviti-ai-superdev-eks-nodegroup-superdev-ng-1` | Managed node group |
| `eksctl-bruviti-ai-superdev-eks-addon-vpc-cni` | VPC CNI addon IRSA |
| `eksctl-bruviti-ai-superdev-eks-addon-iamserviceaccount-kube-system-aws-load-balancer-controller` | ALB controller IRSA |

## Create Cluster

```bash
eksctl create cluster -f eks/cluster-config.yaml
```

Or via Jenkins job `superdev-eks-manage` with `ACTION=create`.

## Delete Cluster

```bash
eksctl delete cluster --name bruviti-ai-superdev-eks --region us-east-1 --wait
```

Or via Jenkins job `superdev-eks-manage` with `ACTION=delete`.
