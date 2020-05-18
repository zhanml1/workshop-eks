# Install Tools For EKS

## Return to [README.md](README.md)

## yum install
```
yum install -y jq
```

## install eksctl
```
curl -OL "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz"
tar -zxf eksctl_$(uname -s)_amd64.tar.gz
mv ./eksctl /usr/bin
eksctl verion

```
## install kubectl
```
# Kubernetes 1.16
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.16.8/2020-04-16/bin/linux/amd64/kubectl
chmod +x ./kubectl
mv ./kubectl /usr/bin
kubectl version
```
