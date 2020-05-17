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
    --without-nodegroup \
    --vpc-public-subnets  subnet-0383b9b2b1eb02306,subnet-002477d91a5ae9439,subnet-0dfcc24d7cd11e31a \
    --vpc-private-subnets subnet-0f0bfa53ee8740cd9,subnet-0e6ed9d372302bbca,subnet-0c6f764aa2c9f610e

eksctl create nodegroup \
    --cluster $CLUSTER_NAME \
    --region $AWS_REGION \
    --version 1.15 \
    --name $CLUSTER_NAME-nodegroup \
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
