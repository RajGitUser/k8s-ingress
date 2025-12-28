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


# 🌐 Kubernetes Ingress Examples

This repository contains Kubernetes Ingress resource examples and related application manifests to demonstrate how to route external HTTP/HTTPS traffic into services running inside a Kubernetes cluster.
Kubernetes Ingress allows you to control traffic routing using rules based on hostnames and paths while leveraging an Ingress Controller to implement the routing. 
Kubernetes

# 📌 About

In Kubernetes, an Ingress is an API resource that defines Rules to route external traffic to services within the cluster.
It provides a more flexible and consolidated way to expose HTTP and HTTPS services than using individual LoadBalancer or NodePort services. 
Kubernetes

To make Ingress resources functional, an Ingress Controller (such as NGINX, AWS ALB, Traefik, etc.) must be deployed in the cluster to interpret the Ingress rules and handle traffic routing. 
Kubernetes

Each application folder contains:

deployment.yaml – Defines the application Deployment.

service.yaml – Exposes the pod(s) internally via a Kubernetes Service.

ingress.yaml – Defines an Ingress resource routing external traffic to the corresponding service.

🚀 Key Concepts
🔹 Kubernetes Ingress

An Ingress manages external access to services in a cluster, typically HTTP/HTTPS. Rules in an Ingress resource allow routing requests based on hostnames or paths (e.g., /app1, /app2). 
Kubernetes

🔹 Ingress Controller

An Ingress Controller is required to make an Ingress resource effective. It watches Ingress resources and configures a load balancer or reverse proxy to implement the routing rules. Popular controllers include NGINX, Traefik, and cloud provider solutions. 
Kubernetes

🧰 Prerequisites

Before applying manifests in this repo:

✔ A running Kubernetes cluster (Minikube, Kind, EKS, GKE, etc.)
✔ An Ingress Controller deployed in the cluster (e.g., NGINX Ingress Controller)
✔ kubectl configured to communicate with your cluster

For example, install the NGINX Ingress Controller:

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/cloud/deploy.yaml

🛠️ Usage
1. Apply an Application and Ingress
kubectl apply -f app1/
kubectl apply -f app2/

2. Verify Ingress
kubectl get ingress
kubectl describe ingress


Traffic matching the host/path rules in the Ingress manifests will be forwarded to the respective services.

🧠 Example Ingress Rule

A typical Ingress resource might look like:

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: app1-svc
            port:
              number: 80


This rule routes requests matching example.com/app1 to the app1-svc service. 
Kubernetes

📌 Why This Is Useful

Provides HTTP/HTTPS routing into Kubernetes clusters. 
Kubernetes

Enables path-based and host-based routing. 
Kubernetes

Consolidates multiple service entry points into a single endpoint. 
Kubernetes

Works with popular Ingress Controllers for production-grade traffic management. 
Kubernetes

🤝 Contributing

Contributions are welcome! You can:

Add more Ingress rules and examples

Include annotations for TLS, rewrites, and security

Add Helm charts for Ingress deployment

Fork the repo

Create a feature branch

Open a Pull Request
