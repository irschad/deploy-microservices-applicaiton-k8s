# Deploy Microservices Application in Kubernetes with Production & Security Best Practices

## Overview
This project involves deploying an online shop application consisting of multiple microservices in a Kubernetes cluster. The deployment adheres to production and security best practices, ensuring scalability, reliability, and secure operations.

## Key Features
- Kubernetes manifests for Deployments and Services for all microservices.
- Deployment of microservices in an Amazon EKS managed Kubernetes cluster.
- Implementation of production and security best practices, including resource management, probes, and replica configurations.

## Technologies Used
- **Kubernetes**: For container orchestration.
- **Amazon EKS**: Managed Kubernetes cluster on AWS.
- **Redis**: In-memory data store for caching and session management.
- **Linux**: Underlying OS for cluster nodes.
- **Linode LKE**: Used as an alternative testing platform.

## Deployment Steps
1. **Create Kubernetes Cluster**  
   Use the following command to create an EKS cluster:
   ```bash
   eksctl create cluster \
     --name online-shop-microservices \
     --region us-east-1 \
     --nodes 3 \
     --node-type t2.small \
     --managed

 **Configure kubectl**
   Export the kubeconfig to connect to the cluster:
   `export KUBECONFIG=/home/ec2-user/.kube/config`

2. **Verify the nodes**:
   `kubectl get nodes`

3. **Create Namespace**
   Isolate resources by creating a namespace for the microservices:
   `kubectl create ns microservices`
   
4. **Deploy Microservices**
   Apply the Kubernetes manifests to deploy the application:
    `kubectl apply -f config.yaml -n microservices`
   
5. **Verify Deployment**
    Check the running pods and services:
    `kubectl get pods -n microservices`
    `kubectl get svc -n microservices`
   
## Production & Security Best Practices
- Pinned Container Image Versions: Ensures consistent and reproducible deployments.
- Liveness and Readiness Probes: Monitors and manages container health.
- Resource Requests and Limits: Allocates resources efficiently and prevents over-utilization.
- Avoid NodePort Services: Ensures better security by using ClusterIP or LoadBalancer types. Here, LoadBalancer type is used for access through web browser. 
- Replicas and High Availability: Configured with more than one replica for fault tolerance.
- Namespace Isolation: Organizes and secures resources using namespaces.
- Labels and Selectors: For better resource organization and management.
- Multiple Worker Nodes: Ensures high availability and load distribution.

  Security Best Practices:
  1. Ensure images are free of vulnerabilities
  2. No Root Access for containers
  3. Update Kubernetes to the latest version


**Configuration Files**
The Kubernetes manifests for Deployments and Services are located in the config.yaml file. 

**Microservices List**
- adservice
- cartservice
- checkoutservice
- currencyservice
- emailservice
- frontend
- paymentservice
- productcatalogservice
- recommendationservice
- redis-cart
- shippingservice
  
Load Balancer
The frontend service is exposed using a LoadBalancer, accessible via the external IP provided by the Kubernetes cluster.







