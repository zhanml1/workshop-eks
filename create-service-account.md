# Create service account for pod

## Return to [README.md](README.md)

## 1. create service account
Check OIDC Provider
```
eksctl create iamserviceaccount --name s3-full-access --namespace default \
    --cluster ${CLUSTER_NAME} --attach-policy-arn arn:aws-cn:iam::aws:policy/AmazonS3FullAccess \
    --approve --override-existing-serviceaccounts --region ${AWS_REGION}
```
## 2. create S3 bucket
```
S3_BUCKET=ekstest20200518
if [ $(aws s3 ls | grep $S3_BUCKET | wc -l) -eq 0 ]; then
    aws s3 mb s3://$S3_BUCKET --region $AWS_REGION
else
    echo "S3 bucket $S3_BUCKET existed, skip creation"
fi
```

## 2. enter pod
```
kubectl get pod
kubectl exec -it <podname> /bin/bash

aws s3 ls s3://<S3_BUCKET>
```
output
```
access denied
```


## 3. update myapp-deployment
```
kubectl edit deployment myapp-deployment
# add "serviceAccountName: s3-full-access" below "terminationGracePeriodSeconds: 30"
```

## 4. re-enter pod
```
kubectl get pod
kubectl exec -it <podname> /bin/bash

aws s3 ls s3://<S3_BUCKET>
```
output
```
no error
```
