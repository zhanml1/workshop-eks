# Create APP/Deployment/Service

## Return to [README.md](README.md)

## 1. make app myapp.yml
update image like xxxxxx.dkr.ecr.ap-southeast-1.amazonaws.com/mytomcat:1
```
cat <<EOF > myapp.yml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
spec:
  selector:
    matchLabels:
      app: myapp
  replicas: 1
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: mytomcat
        image: xxxxxx.dkr.ecr.ap-southeast-1.amazonaws.com/mytomcat:1
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: "myapp-service"
spec:
  selector:
    app: myapp
  type: ClusterIP
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
EOF

```

## 2. build deploy/service
```
kubectl apply -f myapp.yml
```

## 3. check deployment and service
```
kubectl get deploy
kubectl get pod -o wide
kubectl get service -o wide

```
