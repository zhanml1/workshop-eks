# Create EKS Cluster and Nodegroup

## Return to [README.md](README.md)

## Create EKS Cluster and Nodegroup
```
export CLUSTER_NAME=eks-test
export AWS_REGION=ap-southeast-1

eksctl create cluster \
    --name $CLUSTER_NAME \
    --version 1.15 \
    --region $AWS_REGION \
    --nodegroup-name eks-mark-nodes \
    --node-type t3.small \
    --node-volume-size 10 \
    --nodes 1 \
    --nodes-min 1 \
    --nodes-max 4 \
    --alb-ingress-access \
    --managed \
    --asg-access
```
