Install OIDC Provider
'''''''''''''''''''''

eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster roboshop-dev \
    --approve

Download IAM Policy
''''''''''''''''

curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.16.0/docs/install/iam_policy.json


Create IAM Policy
'''''''''''''''''

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json

Create an IAM role and Kubernetes ServiceAccount
''''''''''''''''''''''''''''''''''''''''''''''''

eksctl create iamserviceaccount \
--cluster=roboshop-dev \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::848332098195:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve

Install Helm
''''''''''''
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-4
chmod 700 get_helm.sh
./get_helm.sh

Add EKS CHart Repo
''''''''''''''''''
helm repo add eks https://aws.github.io/eks-charts

Install LBC
'''''''''''
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=roboshop-dev --

Check Drivers are running
'''''''''''''''''''''''''
kubectl get pods -n kube-system