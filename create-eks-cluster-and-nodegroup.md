# Create EKS Cluster and Nodegroup

## Return to [README.md](README.md)

## 1. Create EKS Cluster and Nodegroup
```
export AWS_REGION=ap-southeast-1
export CLUSTER_NAME=eks-test

eksctl create cluster --name=${CLUSTER_NAME} --node-type t3.medium --managed --alb-ingress-access --region=${AWS_REGION}

```
## 2. check node group status
```
kubectl get node
```
output
```
NAME                                                STATUS   ROLES    AGE    VERSION
ip-192-168-14-19.cn-northwest-1.compute.internal    Ready    <none>   4d1h   v1.14.9-eks-1f0ca9
ip-192-168-40-132.cn-northwest-1.compute.internal   Ready    <none>   4d1h   v1.14.9-eks-1f0ca9
```
