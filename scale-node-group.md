# Scale Node Group

## Return to [README.md](README.md)

## 1.scale node group
```
NODE_GROUP=$(eksctl get nodegroup --cluster ${CLUSTER_NAME} --region=${AWS_REGION} -o json | jq -r '.[].Name')
eksctl scale nodegroup --cluster=${CLUSTER_NAME} --nodes=2 --name=${NODE_GROUP} --region=${AWS_REGION}
kubectl get node
```
