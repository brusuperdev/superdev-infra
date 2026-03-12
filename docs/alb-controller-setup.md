# AWS Load Balancer Controller Setup

## Overview

AWS Load Balancer Controller manages ALBs for Kubernetes Ingress resources. A single ALB handles path-based routing for all services under `apps.brusuperdev.com`.

## Details

| Item | Value |
|---|---|
| Controller Version | Helm chart `eks/aws-load-balancer-controller` |
| Namespace | `kube-system` |
| Service Account | `aws-load-balancer-controller` (IRSA) |
| IAM Role | `AmazonEKSLoadBalancerControllerRole` |
| IAM Policy | `AWSLoadBalancerControllerIAMPolicy` |
| ACM Certificate | `arn:aws:acm:us-east-1:605893374669:certificate/1904e184-d30f-4738-9dfb-463fb726f621` |
| Domain | `apps.brusuperdev.com` |

## Installation Steps (one-time)

### 1. Create IAM Policy

```bash
curl -sL https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.12.0/docs/install/iam_policy.json -o /tmp/alb-iam-policy.json

aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file:///tmp/alb-iam-policy.json
```

### 2. Create IAM Service Account (IRSA)

```bash
eksctl create iamserviceaccount \
  --cluster=bruviti-ai-superdev-eks \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::605893374669:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve \
  --region=us-east-1
```

### 3. Install via Helm

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=bruviti-ai-superdev-eks \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=vpc-0a05f0eb7f3518554
```

### 4. Verify

```bash
kubectl get deployment aws-load-balancer-controller -n kube-system
kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-load-balancer-controller
```

## Routing Architecture

```
apps.brusuperdev.com (DNS CNAME → ALB)
  └── ALB (HTTPS:443, ACM cert)
       ├── /sample-service  → sample-service:80
       ├── /service-b       → service-b:80
       └── /service-c       → service-c:80
```

## Upgrade

```bash
helm upgrade aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=bruviti-ai-superdev-eks \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=vpc-0a05f0eb7f3518554
```

## Uninstall

```bash
helm uninstall aws-load-balancer-controller -n kube-system
```
