# Create EKS Cluster and Nodegroup

## Return to [README.md](README.md)

## 1. get subnet-id
点击左上角Services，搜索VPC。
点击VPC进入。
点击左侧Subnets。
查看Public Subnet x和Private Subnet x的Subnet ID。
用该subnet id更新下面的指令。

## 1. Create EKS Cluster and Nodegroup
```
export AWS_REGION=ap-southeast-1
export CLUSTER_NAME=eks-test

eksctl create cluster --name=${CLUSTER_NAME} --node-type t3.medium --managed --alb-ingress-access --region=${AWS_REGION}

eksctl create cluster \
--name $CLUSTER_NAME \
--version 1.16 \
--region $AWS_REGION \
--vpc-public-subnets subnet-007eef1f319e0419d,subnet-0208715e6e16d9c65,subnet-019306be7ea6a81b1 \
--vpc-private-subnets subnet-05fd021a9624ed86f,subnet-0f8beaf723fa7898a,subnet-03854e189258c653d \
--nodegroup-name $CLUSTER_NAME-nodegroup \
--node-type t3.small \
--node-volume-size 5 \
--nodes 1 \
--nodes-min 1 \
--nodes-max 5 \
--node-private-networking \
--alb-ingress-access \
--managed \
--asg-access \
--full-ecr-access


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
