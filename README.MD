---
## Step-1: Create an IAM OIDC provider.

eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster safety-dev \
    --approve
---\

## Step-2: Download an IAM policy.
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.16.0/docs/install/iam_policy.json
---\

## step-3: Create an IAM policy named AWSLoadBalancerControllerIAMPolicy
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
---\

## Step-4: Create an IAM role and Kubernetes ServiceAccount for the LBC and map above policy
eksctl create iamserviceaccount \
--cluster=safety-dev \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::9960xxxxxx:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve
---\

## step-5: Istall Drivers and Helm
--Install Helm--
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-4
chmod 700 get_helm.sh
./get_helm.sh
---\

## Add the EKS chart repo to Helm
helm repo add eks https://aws.github.io/eks-charts
---\

## Install load balancer controller (drivers)
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=safety-dev --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
---\

## Check Drivers are running
kubectl get pods -n kube-system






