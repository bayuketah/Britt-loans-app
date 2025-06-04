## ** EKS Monitoring Stack Setup (Prometheus + Grafana + Metrics Server) **

This guide walks you through setting up a full monitoring stack (Prometheus, Grafana, and Metrics Server) on an Amazon EKS cluster using Helm. 

## 📦 Prerequisites

- AWS CLI configured
- eksctl installed
- kubectl installed
- helm installed

---

## 🔧 EKS Cluster Creation 
eksctl create cluster --name britt-cluster --region us-east-1 --node-type t3.medium --nodes 2 --zones us-east-1a,us-east-1b

# Confirm cluster is active
eksctl get cluster --region us-east-1

# Delete cluster Resources - Cleans System
kubectl delete all --all

# Delete cluster 
eksctl delete cluster --name britt-cluster1 --region us-east-1

---

## ** Download and install Helm **
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3

### Give execute permission
- chmod 700 get_helm.sh
- ./get_helm.sh

## Check if Helm is installed in cluster
helm

## Check Helm Repo
helm repo ls

## Add the official Prometheus Helm chart repo to your Helm config
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

## Update your repos
helm repo update

## INSTALL PROMETHEUS & GRAFANA 
helm install monitoring prometheus-community/kube-prometheus-stack

## Check if Prometheus + Grafana pods are running
kubectl get pods

## ** Grafana Server Credentials
- Username: admin
- Password: prom-operator

### NB: 
-Prometheus runs on port 9090
-Grafana runs on Port 80

---

## ✅ Expose Prometheus
kubectl edit svc monitoring-kube-prometheus-prometheus
- Find this section:
type: ClusterIP
- Change it to:
type: LoadBalancer

## ✅ Expose Grafana
kubectl edit svc monitoring-grafana
- Find this section:
type: ClusterIP
- Change it to:
type: LoadBalancer

## 🧠 To Save & Exit in vi or vim:
Once you’ve changed type: ClusterIP to type: LoadBalancer
- Esc → :wq

## ✅ Next run
kubectl get svc

### Look for EXTERNAL-IP to show up next to:
monitoring-grafana
monitoring-kube-prometheus-prometheus

## ✅ Access Prometheus and Grafana URLs Using the Load Balancers
-Prometheus URL : LBR-DNS:9090/
-Grafana URL : LBR-DNS/

### NOTE
- Prometheus runs on port 9090
- Grafana runs on port 80
