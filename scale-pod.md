# Scale pod

## Return to [README.md](README.md)

## 1. manually scale pod
```
kubectl scale --replicas=10 deployment/myapp-deployment
kubectl get pod -o wide
```
Check EC2>LOAD BALANCING>Target Group>Targets

## 2. check pod status
There are 4 pod in Pending status.
```
kubectl get pod
kubectl get pod -o json | jq -r '.items[] | select(.status.phase=="Pending")' | jq -r '.metadata.name'
kubectl get events
```
## 3. scale node group
scale node instance to 2
```
NODE_GROUP=$(eksctl get nodegroup --cluster ${CLUSTER_NAME} --region=${AWS_REGION} -o json | jq -r '.[].Name')
eksctl scale nodegroup --cluster=${CLUSTER_NAME} --nodes=2 --name=${NODE_GROUP} --region=${AWS_REGION}
kubectl get node
```
## 4. check pod status
All pods are in Running status.
```
kubectl get pod
```

## 5. create HPA
```
curl -sL https://api.github.com/repos/kubernetes-sigs/metrics-server/tarball/v0.3.6 -o metrics-server-v0.3.6.tar.gz
tar -xzf metrics-server-v0.3.6.tar.gz
kubectl apply -f kubernetes-sigs-metrics-server-d1f4f6f/deploy/1.8+/
kubectl get pod --all-namespaces
```
## 6. scale down pod
```
kubectl scale --replicas=1 deployment/myapp-deployment
```

## 7. set limit for pod
```
kubectl set resources deployment myapp-deployment --limits=cpu=200m,memory=128Mi
```

## 8. set autoscale
```
kubectl autoscale deployment myapp-deployment --cpu-percent=30 --min=1 --max=20
```

## 9. monitor HPA
```
kubectl top pod
kubectl get hpa --watch
```

## 10. load test
```
ab -c 3 -n 2000000 http://xxxxxx.ap-southeast-1.elb.amazonaws.com/ 
```

## 11. create cluster autoscaler

### create cluster autoscaler
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml
```

### add annotation
```
kubectl -n kube-system annotate deployment.apps/cluster-autoscaler cluster-autoscaler.kubernetes.io/safe-to-evict="false"
```

### edit the Cluster Autoscaler deployment 
```
kubectl -n kube-system edit deployment.apps/cluster-autoscaler
```
- replace <YOUR CLUSTER NAME> with your cluster's name
- add --balance-similar-node-groups
- add --skip-nodes-with-system-pods=false
  
```
spec:
      containers:
      - command:
        - ./cluster-autoscaler
        - --v=4
        - --stderrthreshold=info
        - --cloud-provider=aws
        - --skip-nodes-with-local-storage=false
        - --expander=least-waste
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/<YOUR CLUSTER NAME>
        - --balance-similar-node-groups
        - --skip-nodes-with-system-pods=false
```

### update image
```
kubectl -n kube-system set image deployment.apps/cluster-autoscaler cluster-autoscaler=asia.gcr.io/k8s-artifacts-prod/autoscaling/cluster-autoscaler:v1.16.5
```

### cluster autoscaler logs
```
kubectl -n kube-system logs -f deployment.apps/cluster-autoscaler
```
