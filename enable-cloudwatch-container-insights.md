# Enable cloudwatch container insights

## 1. add policy to role of EC2
- find EC2 role
- modify the role
- add "CloudWatchAgentServerPolicy"

## 2. add cloudwatch agent and fluentd agent
modify cluster-name and cluster-region
```
curl https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/quickstart/cwagent-fluentd-quickstart.yaml | sed "s/{{cluster_name}}/cluster-name/;s/{{region_name}}/cluster-region/" | kubectl apply -f -
```
