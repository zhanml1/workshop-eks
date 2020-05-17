# Create EKS Cluster and Nodegroup

## Return to [README.md](README.md)

## Create EKS Cluster and Nodegroup
```
export AWS_REGION=ap-southeast-1
export CLUSTER_NAME=eks-test

eksctl create cluster --name=${CLUSTER_NAME} --node-type t3.medium --managed --alb-ingress-access --region=${AWS_REGION}

```
