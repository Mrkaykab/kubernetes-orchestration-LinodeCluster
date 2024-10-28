# kubernetes-orchestration-LinodeCluster
This guide is a walkthrough of the steps I took to set up a Kubernetes cluster on Linode, deploy applications. If you're looking to recreate this setup, follow along!

---

## Prerequisites
Here’s what you’ll need before starting:

- **Linode CLI**: To interact with Linode’s API.
- **kubectl**: To manage your Kubernetes cluster.
- **Helm**: To install and manage Kubernetes applications.
---

## Steps
### Step 1: Set Up Linode CLI and Authenticate
First, I installed the **Linode CLI** and authenticated myself to access the Linode API:

1. **Install Linode CLI**: I started by installing the Linode CLI using `snap` , as it’s quick and easy.
```
snap install linode-cli
```

1. **Configure Linode CLI**: After installation, I configured the CLI with my Linode API token to authenticate. This step required launching a browser, which didn’t work right away on the terminal, so I opted to enter the API token manually. 
```
linode-cli configure --token
```
---

### Step 2: Create a Kubernetes Cluster on Linode
With the Linode CLI set up, the next step was to create a Kubernetes cluster. I used Linode's LKE (Linode Kubernetes Engine) to spin up the cluster.

1. **Create the Cluster**: I logged in on my linode account to create a new cluster with two nodes, each a standard Linode instance. After a few seconds, the cluster was created, and I noted down the **cluster ID** for future commands.
![linode k8s computex-cluster](https://github.com/user-attachments/assets/37a28619-bdc2-40d5-9371-fd2a8cb39819)

3. **Get the Kubeconfig File**: To manage the cluster with `kubectl` , I copied the Kubeconfig file from my linode cluster and paste in config-linode.yaml. I ran the command below to save the kubeconfig file and export it for `kubectl`  to recognize.
```
nano ~/.kube/config-linode.yaml
export KUBECONFIG=~/.kube/config-linode.yaml
```
![linode kube config](https://github.com/user-attachments/assets/ddd5b83b-eff7-40bd-a808-50a3852ad8c2)

1. **Verify the Cluster**: I checked that `kubectl`  could access the cluster by listing the nodes.
```
kubectl get nodes
```
---

### Step 3: Deploy Applications on the Cluster
With Kubernetes ready, I deployed my applications (frontend and backend) to the cluster.

1. **Define Deployment Files**: I created separate YAML files for the frontend and backend deployments.
**Backend Deployment (**`**backend-deployment.yaml**` **)**:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: computex-backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: computex-backend
  template:
    metadata:
      labels:
        app: computex-backend
    spec:
      containers:
        - name: backend
          image: mrkaykab/computex-backend:latest
          ports:
            - containerPort: 8000
```
**Frontend Deployment (frontend-deployment.yaml):**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: computex-frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: computex-frontend
  template:
    metadata:
      labels:
        app: computex-frontend
    spec:
      containers:
        - name: frontend
          image: mrkaykab/computex-frontend:latest
          ports:
            - containerPort: 80
```
1. **Apply Deployments**: I deployed both services by applying the YAML files:
```
kubectl apply -f backend-deployment.yaml
kubectl apply -f frontend-deployment.yaml
```
![kubectl apply frontend-backend k8s](https://github.com/user-attachments/assets/5955b313-3bf1-4d32-bc5f-daf188dfc5f5)


1. **Confirm Deployments**: Once deployed, I checked that both services were running.
```
kubectl get deployments
kubectl get pods
```
![linode k8s pods](https://github.com/user-attachments/assets/ce64c928-070c-4044-a635-f9a6e7bc78f3)
![linode k8s deployment](https://github.com/user-attachments/assets/4442e1ef-9749-4ba9-82b0-1e9660a97d80)
![linode kubernetes dashboard](https://github.com/user-attachments/assets/54ebb26b-25ba-4e4e-b290-6b27b40a640f)


---

And that's it! With this setup, I now have a fully functional Kubernetes environment on Linode, with my react-django-app running on it.

![docker frontend running](https://github.com/user-attachments/assets/b4534e33-270f-435a-99cd-c94102364c72)

