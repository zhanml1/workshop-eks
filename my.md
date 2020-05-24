# My us-east-1 configuration

## 1. ENV
```
export CLUSTER_NAME=eks-mark
export AWS_REGION=us-east-1

export ECR_URI=xxxxxx.dkr.ecr.us-east-1.amazonaws.com/webdemo

```
## 2. docker build
```
docker build -t webdemo .
docker images

$(aws ecr get-login --no-include-email --region us-east-1)
docker tag webdemo:latest $ECR_URI:1
docker push $ECR_URI:1
```

## 3. APP/Service yaml
```
cat <<EOF > webdemo.yml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webdemo-deployment
  labels:
    app: webdemo
spec:
  selector:
    matchLabels:
      app: webdemo
  replicas: 1
  template:
    metadata:
      labels:
        app: webdemo
    spec:
      containers:
      - name: webdemo
        image: xxxxxx.dkr.ecr.ap-southeast-1.amazonaws.com/mytomcat:1
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: "webdemo-service"
spec:
  selector:
    app: webdemo
  type: ClusterIP
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
EOF
```
### add liveness prove
```
cat <<EOF > webdemo.yml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webdemo-deployment
  labels:
    app: webdemo
spec:
  selector:
    matchLabels:
      app: webdemo
  replicas: 1
  template:
    metadata:
      labels:
        app: webdemo
    spec:
      containers:
      - name: webdemo
        image: xxxxxx.dkr.ecr.ap-southeast-1.amazonaws.com/mytomcat:1
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: "webdemo-service"
spec:
  selector:
    app: webdemo
  type: ClusterIP
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
EOF
```

## 4. build deploy/service
```
kubectl apply -f webdemo.yml
```

## 5. check deployment and service
```
kubectl get deploy
kubectl get pod -o wide
kubectl get service -o wide

```
## 6. image update
```
kubectl set image deploy webdemo-deployment webdemo=xxxxxx.dkr.ecr.us-east-1.amazonaws.com/webdemo:10
```

## 7. [Create ALB Ingress Controller](create-alb.md)

## 8. EKS Security Group
### Node Group Security Group
```
aws eks describe-cluster --name $CLUSTER_NAME --region $AWS_REGION --query cluster.resourcesVpcConfig.clusterSecurityGroupId
```
The instance of nodegroup is attached with this SG.
```
output
sg-017c49b27e7557992	eks-cluster-sg-eks-mark-553914355
ingress
All TCP	TCP	0 - 65535	sg-04b9e36e8065ccf4d (874fd01e-default-albingres-d740)	-
All traffic	All	All	sg-017c49b27e7557992 (eks-cluster-sg-eks-mark-553914355)	-
All traffic	All	All	sg-0c797c28280410c7d (eksctl-eks-mark-cluster-ClusterSharedNodeSecurityGroup-1GYLHLRT0MUSE)	Allow unmanaged nodes to communicate with control plane (all ports)
```
### Cluster Security Group includes below and above
```
aws eks describe-cluster --name $CLUSTER_NAME --region $AWS_REGION --query cluster.resourcesVpcConfig.securityGroupIds
```
```
output
sg-0440997b4c0232f5d	eksctl-eks-mark-cluster-ControlPlaneSecurityGroup-1OI1V9VE269SG
ingress
null
```
### ALB Security Group
```
sg-04b9e36e8065ccf4d	874fd01e-default-albingres-d740
```




