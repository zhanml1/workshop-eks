# Create service account for pod

## Return to [README.md](README.md)

## 1. create service account
Check OIDC Provider
```
eksctl create iamserviceaccount --name s3-full-access --namespace default \
    --cluster ${CLUSTER_NAME} --attach-policy-arn arn:aws-cn:iam::aws:policy/AmazonS3FullAccess \
    --approve --override-existing-serviceaccounts --region ${AWS_REGION}
```

## 2. update myapp-deployment
```
kubectl edit deployment myapp-deployment
# add servicename: s3-full-access
```


