# Jenkins Jobs

## URL

`https://ci.brusuperdev.com:5001`

## Jobs

### superdev-eks-manage

Manages the EKS cluster lifecycle.

| Parameter | Values | Description |
|---|---|---|
| ACTION | `status`, `create`, `create-nodegroup`, `delete`, `get-nodes`, `get-pods` | Action to perform |

### sample-service-build

Builds Docker image from `brusuperdev/sample-service` repo and pushes to ECR.

- Clones repo → Docker build → ECR login → Push with build number tag + latest
- ECR: `605893374669.dkr.ecr.us-east-1.amazonaws.com/bruviti-ai-superdev-central`

### service-deploy

Deploys any service to EKS using the shared base Helm chart from `superdev-infra`.

| Parameter | Description |
|---|---|
| SERVICE_NAME | Service name (e.g., `sample-service`) — must match a values file in `helm/values/` |
| IMAGE_TAG | Docker image tag to deploy |
| NAMESPACE | Kubernetes namespace (default: `superdev`) |

### test-s3-access

Smoke test — verifies Jenkins EC2 IAM role can read/write S3 buckets.

### sandbox-test-pipeline

Smoke test — verifies GitHub clone and push with `brusuperdev` credentials.

## Credentials

| ID | Type | Purpose |
|---|---|---|
| `github-brusuperdev` | Username/Password | Git SCM checkout |
| `github-brusuperdev-token` | Secret Text | GitHub API calls |
