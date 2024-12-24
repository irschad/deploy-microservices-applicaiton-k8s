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
     --node-type t2.small \
     --managed

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
   
4. **Create Namespace**
   Isolate resources by creating a namespace for the microservices:
   ```bash
   kubectl create ns microservices
   namespace/microservices created
   ```
   
6. **Deploy Microservices**
   Apply the Kubernetes config to deploy the application:
    `kubectl apply -f config.yaml -n microservices`
   
7. **Verify Deployment**
    Check the running pods and services:
    `kubectl get pods -n microservices`
    `kubectl get svc -n microservices`

8. **Update Deployment for best practices**, including adding liveness and readiness probes, adding resource requests and limits, avoiding NodePort, and adding one replica.
   ```bash   
   kubectl get pods -n microservices
   NAME                                     READY   STATUS    RESTARTS   AGE
   adservice-5f488b4f8b-726ft               1/1     Running   0          45s
   adservice-5f488b4f8b-hp7fl               1/1     Running   0          45s
   cartservice-68f8747f94-nrljl             1/1     Running   0          45s
   cartservice-68f8747f94-nv26r             1/1     Running   0          45s
   checkoutservice-86bdcfbbfc-2drx9         1/1     Running   0          45s
   checkoutservice-86bdcfbbfc-pblrt         1/1     Running   0          45s
   currencyservice-6f7fd6989-96c5f          1/1     Running   0          46s
   currencyservice-6f7fd6989-wcpq9          1/1     Running   0          45s
   emailservice-6df48986c8-74tn9            1/1     Running   0          46s
   frontend-55cf69d447-2x2q5                1/1     Running   0          44s
   frontend-55cf69d447-z4v4j                1/1     Running   0          45s
   paymentservice-c5776df6f-ddw55           1/1     Running   0          46s
   paymentservice-c5776df6f-gw6hc           1/1     Running   0          46s
   productcatalogservice-8d4f48b75-qkpc7    1/1     Running   0          46s
   productcatalogservice-8d4f48b75-t8qb8    1/1     Running   0          46s
   recommendationservice-69459db9c9-886tn   1/1     Running   0          46s
   recommendationservice-69459db9c9-v25lr   1/1     Running   0          46s
   redis-cart-8c5bbbccf-6zmwd               1/1     Running   0          45s
   redis-cart-8c5bbbccf-wn9ft               1/1     Running   0          45s
   shippingservice-5cc877bc4c-ghpbv         1/1     Running   0          45s
   shippingservice-5cc877bc4c-rzjzm         1/1     Running   0          45s
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







