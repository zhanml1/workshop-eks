# Create ALB Ingress Controller

## Return to [README.md](README.md)

## 1. Create EKS OIDC Provider
```
eksctl utils associate-iam-oidc-provider --cluster=${CLUSTER_NAME} --approve --region ${AWS_REGION}
```

## 2. Create IAM Policy 

### create iam-policy-v1.1.5.json
```
cat <<EOF > iam-policy-v1.1.5.json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "acm:DescribeCertificate",
        "acm:ListCertificates",
        "acm:GetCertificate"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ec2:AuthorizeSecurityGroupIngress",
        "ec2:CreateSecurityGroup",
        "ec2:CreateTags",
        "ec2:DeleteTags",
        "ec2:DeleteSecurityGroup",
        "ec2:DescribeAccountAttributes",
        "ec2:DescribeAddresses",
        "ec2:DescribeInstances",
        "ec2:DescribeInstanceStatus",
        "ec2:DescribeInternetGateways",
        "ec2:DescribeNetworkInterfaces",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeSubnets",
        "ec2:DescribeTags",
        "ec2:DescribeVpcs",
        "ec2:ModifyInstanceAttribute",
        "ec2:ModifyNetworkInterfaceAttribute",
        "ec2:RevokeSecurityGroupIngress"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "elasticloadbalancing:AddListenerCertificates",
        "elasticloadbalancing:AddTags",
        "elasticloadbalancing:CreateListener",
        "elasticloadbalancing:CreateLoadBalancer",
        "elasticloadbalancing:CreateRule",
        "elasticloadbalancing:CreateTargetGroup",
        "elasticloadbalancing:DeleteListener",
        "elasticloadbalancing:DeleteLoadBalancer",
        "elasticloadbalancing:DeleteRule",
        "elasticloadbalancing:DeleteTargetGroup",
        "elasticloadbalancing:DeregisterTargets",
        "elasticloadbalancing:DescribeListenerCertificates",
        "elasticloadbalancing:DescribeListeners",
        "elasticloadbalancing:DescribeLoadBalancers",
        "elasticloadbalancing:DescribeLoadBalancerAttributes",
        "elasticloadbalancing:DescribeRules",
        "elasticloadbalancing:DescribeSSLPolicies",
        "elasticloadbalancing:DescribeTags",
        "elasticloadbalancing:DescribeTargetGroups",
        "elasticloadbalancing:DescribeTargetGroupAttributes",
        "elasticloadbalancing:DescribeTargetHealth",
        "elasticloadbalancing:ModifyListener",
        "elasticloadbalancing:ModifyLoadBalancerAttributes",
        "elasticloadbalancing:ModifyRule",
        "elasticloadbalancing:ModifyTargetGroup",
        "elasticloadbalancing:ModifyTargetGroupAttributes",
        "elasticloadbalancing:RegisterTargets",
        "elasticloadbalancing:RemoveListenerCertificates",
        "elasticloadbalancing:RemoveTags",
        "elasticloadbalancing:SetIpAddressType",
        "elasticloadbalancing:SetSecurityGroups",
        "elasticloadbalancing:SetSubnets",
        "elasticloadbalancing:SetWebACL"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "iam:CreateServiceLinkedRole",
        "iam:GetServerCertificate",
        "iam:ListServerCertificates"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "cognito-idp:DescribeUserPoolClient"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "waf-regional:GetWebACLForResource",
        "waf-regional:GetWebACL",
        "waf-regional:AssociateWebACL",
        "waf-regional:DisassociateWebACL"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "tag:GetResources",
        "tag:TagResources"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "waf:GetWebACL"
      ],
      "Resource": "*"
    }
  ]
}
EOF

```

### create IAM policy
```
aws iam create-policy --policy-name ALBIngressControllerIAMPolicy \
  --policy-document file://./iam-policy-v1.1.5.json --region ${AWS_REGION}
```
### export ENV 
```
POLICY_NAME=$(aws iam list-policies --query 'Policies[?PolicyName==`ALBIngressControllerIAMPolicy`].Arn' --output text --region ${AWS_REGION})
```

## 3.create service account
```
eksctl create iamserviceaccount \
       --cluster=${CLUSTER_NAME} \
       --namespace=kube-system \
       --name=alb-ingress-controller \
       --attach-policy-arn=${POLICY_NAME} \
       --override-existing-serviceaccounts \
       --approve
```

## 4.create RBAC

### create rbac-role-v1.1.5.yaml
```
cat <<EOF > rbac-role-v1.1.5.yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    app.kubernetes.io/name: alb-ingress-controller
  name: alb-ingress-controller
rules:
  - apiGroups:
      - ""
      - extensions
    resources:
      - configmaps
      - endpoints
      - events
      - ingresses
      - ingresses/status
      - services
    verbs:
      - create
      - get
      - list
      - update
      - watch
      - patch
  - apiGroups:
      - ""
      - extensions
    resources:
      - nodes
      - pods
      - secrets
      - services
      - namespaces
    verbs:
      - get
      - list
      - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    app.kubernetes.io/name: alb-ingress-controller
  name: alb-ingress-controller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: alb-ingress-controller
subjects:
  - kind: ServiceAccount
    name: alb-ingress-controller
    namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    app.kubernetes.io/name: alb-ingress-controller
  name: alb-ingress-controller
  namespace: kube-system
...
EOF

```

### create RBAC
```
kubectl apply -f rbac-role-v1.1.5.yaml
```

## 5. create ALB APP

### get vpc-id
```
eksctl get cluster --name eks-test -o json | jq -r '.[].ResourcesVpcConfig.VpcId'
```

### create alb-ingress-controller-v1.1.5.yaml
modify              
  - --cluster-name=xxx
  - --aws-vpc-id=xxx
  - --aws-region=xxx
```
cat <<EOF > alb-ingress-controller-v1.1.5.yaml
# Application Load Balancer (ALB) Ingress Controller Deployment Manifest.
# This manifest details sensible defaults for deploying an ALB Ingress Controller.
# GitHub: https://github.com/kubernetes-sigs/aws-alb-ingress-controller
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app.kubernetes.io/name: alb-ingress-controller
  name: alb-ingress-controller
  # Namespace the ALB Ingress Controller should run in. Does not impact which
  # namespaces it's able to resolve ingress resource for. For limiting ingress
  # namespace scope, see --watch-namespace.
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: alb-ingress-controller
  template:
    metadata:
      labels:
        app.kubernetes.io/name: alb-ingress-controller
    spec:
      containers:
        - name: alb-ingress-controller
          args:
            # Limit the namespace where this ALB Ingress Controller deployment will
            # resolve ingress resources. If left commented, all namespaces are used.
            # - --watch-namespace=your-k8s-namespace

            # Setting the ingress-class flag below ensures that only ingress resources with the
            # annotation kubernetes.io/ingress.class: "alb" are respected by the controller. You may
            # choose any class you'd like for this controller to respect.
            - --ingress-class=alb

            # REQUIRED
            # Name of your cluster. Used when naming resources created
            # by the ALB Ingress Controller, providing distinction between
            # clusters.
            - --cluster-name=eks-test

            # AWS VPC ID this ingress controller will use to create AWS resources.
            # If unspecified, it will be discovered from ec2metadata.
            - --aws-vpc-id=vpc-07d32d2994e87f2cd

            # AWS region this ingress controller will operate in.
            # If unspecified, it will be discovered from ec2metadata.
            # List of regions: http://docs.aws.amazon.com/general/latest/gr/rande.html#vpc_region
            - --aws-region=ap-southeast-1

            # Enables logging on all outbound requests sent to the AWS API.
            # If logging is desired, set to true.
            # - --aws-api-debug
            # Maximum number of times to retry the aws calls.
            # defaults to 10.
            # - --aws-max-retries=10
          # env:
            # AWS key id for authenticating with the AWS API.
            # This is only here for examples. It's recommended you instead use
            # a project like kube2iam for granting access.
            #- name: AWS_ACCESS_KEY_ID
            #  value: KEYVALUE

            # AWS key secret for authenticating with the AWS API.
            # This is only here for examples. It's recommended you instead use
            # a project like kube2iam for granting access.
            #- name: AWS_SECRET_ACCESS_KEY
            #  value: SECRETVALUE
          # Repository location of the ALB Ingress Controller.
          image: docker.io/amazon/aws-alb-ingress-controller:v1.1.5
      serviceAccountName: alb-ingress-controller
EOF

```

### create ALB deployment
```
kubectl apply -f alb-ingress-controller-v1.1.5.yaml
```

## 6. create ingress for service

### create alb-ingress.yml
```
cat <<EOF > alb-ingress.yml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: "alb-ingress"
  namespace: "default"
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
  labels:
    app: myapp
spec:
  rules:
    - http:
        paths:
          - path: /*
            backend:
              serviceName: "myapp-service"
              servicePort: 8080
EOF
```
### create ALB
```
kubectl apply -f alb-ingress.yml
```

## 7. get ALB URL
```
kubectl get ingress
```
output
```
AME          HOSTS   ADDRESS                                                                       PORTS   AGE
alb-ingress   *       xxx.ap-southeast-1.elb.amazonaws.com   80      13m
```
browser
```
http://xxx.ap-southeast-1.elb.amazonaws.com
```
