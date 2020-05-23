# My us-east-1 configuration

## 1. ENV
```
export ECR_URI=773710653318.dkr.ecr.us-east-1.amazonaws.com/webdemo
export AWS_REGION=us-east-1
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
## 4. build deploy/service
```
kubectl apply -f myapp.yml
```

## 5. check deployment and service
```
kubectl get deploy
kubectl get pod -o wide
kubectl get service -o wide

```


