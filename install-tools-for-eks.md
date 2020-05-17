# Install Tools For EKS

## Return to [README.md](README.md)

## install eksctl
```
curl -OL "https://github.com/weaveworks/eksctl/releases/download/0.15.0-rc.2/eksctl_$(uname -s)_amd64.tar.gz"
tar -zxf eksctl_$(uname -s)_amd64.tar.gz
mv ./eksctl /usr/bin

```
## install kubectl
```
curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.15.10/2020-02-22/bin/linux/amd64/kubectl
chmod +x ./kubectl
mv ./kubectl /usr/bin
```
