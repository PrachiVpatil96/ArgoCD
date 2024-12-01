# ArgoCD Deployment Guide

This guide explains how to set up **ArgoCD** on your Kubernetes cluster and manage your applications using it.

---

## Prerequisites

- Kubernetes cluster up and running.
- `kubectl` installed and configured to interact with your cluster.
- `manifest` files for your application
- Admin access to the cluster.

---

# Step 1: Set Up the Git Repository

## Create a Git Repository

1. **Create a New Repository**:  
   Create a new Git repository on your preferred Git hosting service, such as:

   - [GitHub](https://github.com/)
   - [GitLab](https://gitlab.com/)
   - [Bitbucket](https://bitbucket.org/)

2. **Clone the Repository**:  
   Clone the newly created repository to your local machine using the following command:

   ```bash
   git clone <repository-url>
   cd <repository-name>
   ```

## Add a Sample Kubernetes Manifest

### Example: SkateBoard Application

1. **Create a File**:  
   Create a file named `Deployment.yaml` in your repository.

2. **Add the Following Content**:  
   Copy and paste the below YAML manifest into the `Deployment.yaml` file.

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
   name: skate-app
   labels:
       app: webapp
   spec:
   minReadySeconds: 5
   replicas: 2
   selector:
       matchLabels:
       app: webapp
   template:
       metadata:
       labels:
           app: webapp
       spec:
       containers:
           - name: nginx
           image: prachiii123/skate:1.0
           ports:
               - containerPort: 80
               protocol: TCP
           resources:
               requests:
               memory: 50Mi
               cpu: 0.5m
               limits:
               memory: 100Mi
               cpu: 0.5m

    ---
       apiVersion: v1
       kind: Service
       metadata:
       name: webapp-service
       spec:
       type: NodePort
       selector:
           app: webapp
       ports:
           - port: 80
           targetPort: 80
   ```

3. Push the Files to Git:
   ```bash
       git add .
       git commit -m "Add SkateBoard Kubernetes manifests"
       git push origin main
   ```

# Step 2: Install ArgoCD

### Option 1: Install via YAML Manifest

Run the following command to install ArgoCD:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

# Step 3: Expose ArgoCD Server

### Option 1: Using a LoadBalancer

    Edit the ArgoCD server service to expose it via LoadBalancer.
    ```bash
    kubectl edit svc argocd-server -n argocd
    ```

### Option 2: Using Port Forwarding

1. **Run the Following Command**:  
   Use the command below to port-forward the ArgoCD server service to your local machine:

   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```
