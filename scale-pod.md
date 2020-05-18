# Scale pod

## Return to [README.md](README.md)

## 1. manually scale pod
```
kubectl scale --replicas=5 deployment/myapp-deployment
kubectl get pod -o wide
```
Check EC2>LOAD BALANCING>Target Group>Targets

## 2. create HPA
```
curl -sL https://api.github.com/repos/kubernetes-sigs/metrics-server/tarball/v0.3.6 -o metrics-server-v0.3.6.tar.gz
tar -xzf metrics-server-v0.3.6.tar.gz
kubectl apply -f kubernetes-sigs-metrics-server-d1f4f6f/deploy/1.8+/
kubectl get pod --all-namespaces
```

## 3. set limit for pod
```
kubectl set resources deployment myapp-deployment --limits=cpu=200m,memory=128Mi
```
## 4. set autoscale
```
kubectl autoscale deployment myapp-deployment --cpu-percent=30 --min=1 --max=10
```

## 5. monitor HPA
```
kubectl top pod
kubectl get hpa --watch
```

## 6. load test
```
ab -c 5 -n 200000 http://39445c63-default-albingres-d740-1150759180.ap-southeast-1.elb.amazonaws.com 
```
