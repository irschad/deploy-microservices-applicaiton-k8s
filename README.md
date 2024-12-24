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

## Deployment Steps
1. **Create Kubernetes Cluster**  
   Use the following command to create an EKS cluster:
    ```bash
    eksctl create cluster \
      --name online-shop-microservices \
      --region us-east-1 \
      --nodes 3 \
      --node-type t2.medium \
      --managed
    ```
   Export the kubeconfig to connect to the cluster:
    `export KUBECONFIG=/home/ec2-user/.kube/config`

2. **Verify the cluster nodes**:
    ```bash
    kubectl get nodes
    NAME                             STATUS   ROLES    AGE     VERSION
    ip-192-168-35-97.ec2.internal    Ready    <none>   2m56s   v1.30.7-eks-59bf375
    ip-192-168-51-198.ec2.internal   Ready    <none>   2m56s   v1.30.7-eks-59bf375
    ip-192-168-8-199.ec2.internal    Ready    <none>   3m5s    v1.30.7-eks-59bf375
    ```
   
4. **Create Namespace:**
   Isolate resources by creating a namespace for the microservices:
    ```bash
    kubectl create ns microservices
    namespace/microservices created
    ```
   
6. **Deploy Microservices:**
     Apply the Kubernetes config to deploy the application:
    `kubectl apply -f config.yaml -n microservices`
   
7. **Verify Deployment:**
    Check the running pods and services:
    `kubectl get pods -n microservices`
    `kubectl get svc -n microservices`

8. **Update Deployment for best practices**, including adding liveness & readiness probes, adding resource requests & limits, avoiding NodePort by replacing it with LoadBalancer, and adding one replica for each pod.
   ```bash   
   kubectl get pods -n microservices
   NAME                                     READY   STATUS    RESTARTS   AGE
   adservice-584d47dcbb-6hznn               1/1     Running   0          16s
   adservice-584d47dcbb-86zdj               1/1     Running   0          16s
   cartservice-68f8747f94-6929q             1/1     Running   0          90m
   cartservice-68f8747f94-wjmsx             1/1     Running   0          90m
   checkoutservice-86bdcfbbfc-4q7s4         1/1     Running   0          90m
   checkoutservice-86bdcfbbfc-zknjm         1/1     Running   0          90m
   currencyservice-6f7fd6989-ghlpn          1/1     Running   0          90m
   currencyservice-6f7fd6989-q8zz6          1/1     Running   0          90m
   emailservice-bb589dbcd-2mc4m             1/1     Running   0          90m
   emailservice-bb589dbcd-dvrr6             1/1     Running   0          90m
   frontend-55cf69d447-7w69j                1/1     Running   0          90m
   frontend-55cf69d447-qz79r                1/1     Running   0          90m
   paymentservice-c5776df6f-l47v4           1/1     Running   0          90m
   paymentservice-c5776df6f-vqxdc           1/1     Running   0          90m
   productcatalogservice-8d4f48b75-g6shq    1/1     Running   0          90m
   productcatalogservice-8d4f48b75-ghvx9    1/1     Running   0          90m
   recommendationservice-69459db9c9-lr62l   1/1     Running   0          90m
   recommendationservice-69459db9c9-x45pb   1/1     Running   0          90m
   redis-cart-8c5bbbccf-cwq9s               1/1     Running   0          90m
   redis-cart-8c5bbbccf-zmz6p               1/1     Running   0          90m
   shippingservice-5cc877bc4c-2v84q         1/1     Running   0          90m
   shippingservice-5cc877bc4c-cm6mr         1/1     Running   0          90m
   ```

   ```bash
   kubectl get svc -n microservices
   NAME                    TYPE           CLUSTER-IP       EXTERNAL-IP                                                               PORT(S)        AGE
   adservice               ClusterIP      10.100.135.97    <none>                                                                    9555/TCP       2m3s
   cartservice             ClusterIP      10.100.107.1     <none>                                                                    7070/TCP       2m3s
   checkoutservice         ClusterIP      10.100.243.89    <none>                                                                    5050/TCP       2m3s
   currencyservice         ClusterIP      10.100.53.207    <none>                                                                    7000/TCP       2m4s
   emailservice            ClusterIP      10.100.195.124   <none>                                                                    5000/TCP       2m4s
   frontend                LoadBalancer   10.100.179.227   ae81806eea8224ab19cc2701eba675f4-1823985534.us-east-1.elb.amazonaws.com   80:32544/TCP   2m3s
   paymentservice          ClusterIP      10.100.152.82    <none>                                                                    50051/TCP      2m4s
   productcatalogservice   ClusterIP      10.100.168.29    <none>                                                                    3550/TCP       2m4s
   recommendationservice   ClusterIP      10.100.177.222   <none>                                                                    8080/TCP       2m4s
   redis-cart              ClusterIP      10.100.155.39    <none>                                                                    6379/TCP       2m3s
   shippingservice         ClusterIP      10.100.26.24     <none>                                                                    50051/TCP      2m3s
   ```
   Note the LoadBalancer URL in above output of `kubectl get svc -n microservices`. 
   
## Production & Security Best Practices
  Following are the best practices:
- Pinned Container Image Versions: Ensures consistent and reproducible deployments.
- Liveness and Readiness Probes: Monitors and manages container health.
- Resource Requests and Limits: Allocates resources efficiently and prevents over-utilization.
- Avoid NodePort Services: Ensures better security by using ClusterIP or LoadBalancer types. Here, LoadBalancer type is used for access through web browser. 
- Replicas and High Availability: Configured with more than one replica for fault tolerance.
- Namespace Isolation: Organizes and secures resources using namespaces.
- Labels and Selectors: For better resource organization and management.
- Multiple Worker Nodes: Ensures high availability and load distribution.

  **More Security Best Practices**:
  1. Ensure images are free of vulnerabilities
  2. No Root Access for containers
  3. Update Kubernetes to the latest version


   **Configuration File:**
   The Kubernetes manifests for Deployments and Services are located in the config.yaml file. 

   **Microservices List:**
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
  
 7. **View the application**:
     The frontend service is exposed using a LoadBalancer, accessible via the external IP provided by the Kubernetes cluster
      i.e. `ae81806eea8224ab19cc2701eba675f4-1823985534.us-east-1.elb.amazonaws.com`.
      Enter this in web browser to see the deployed microservices application.







