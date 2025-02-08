# Kubernetes Deployment on Minikube

Deploy and manage containerized applications using Kubernetes Deployments and Services, ensuring efficient orchestration.

## Tech Stack
- Docker
- Kubernetes (Minikube)
- Helm

## Step 1: Update System Packages
Update your package lists to make sure you are getting the latest version and dependencies.
```bash
sudo apt update
```

## Step 2: Install Docker
Minikube can run a Kubernetes cluster either in a VM or locally via Docker. This guide demonstrates the Docker method.
```bash
sudo apt install -y docker.io
```
Start and enable Docker:
```bash
sudo systemctl enable --now docker
```
Add current user to the Docker group (to use Docker without root):
```bash
sudo usermod -aG docker $USER && newgrp docker
```

## Step 3: Install Minikube
Download the Minikube binary using curl:
```bash
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```
Make it executable and move it into your path:
```bash
chmod +x minikube
sudo mv minikube /usr/local/bin/
```

## Step 4: Install kubectl
Download kubectl, the Kubernetes command-line tool:
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
Make it executable and move it into your path:
```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

## Step 5: Start Minikube
Start Minikube with the following command:
```bash
minikube start --driver=docker --vm=true
```
This command will start a single-node Kubernetes cluster inside a Docker container.

Check the cluster status with:
```bash
minikube status
```

## Step 6: Write a Kubernetes Deployment YAML File
Create a file named `deployment.yaml` with your deployment configuration. Replace `<your-docker-image-name>` with your Docker image name.

Deploy the app using:
```bash
kubectl apply -f deployment.yaml
```

## Step 7: Expose the App Using a Kubernetes Service
Create a file named `service.yaml` with your service configuration.

Apply the service configuration using:
```bash
kubectl apply -f service.yaml
```

### Outcome
A Kubernetes-managed application running on Minikube.

## Step 8: Access the Webpage
Access the deployed webpage at:
```bash
http://localhost:5000
```

## Using Helm

### Install Helm
Install Helm on your local machine:
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

### Creating a Helm Chart for a Project
1. Create a new directory for your Helm chart and navigate into it:
```bash
mkdir webapp
cd webapp
```
2. Initialize a new Helm chart:
```bash
helm create webapp
```
3. Update the `values.yaml` file with the necessary configuration for your Python project:
```yaml
replicaCount: 1
image:
  repository: sweta160431/webapp

service:
  type: LoadBalancer
  port: 5000
  targetPort: 5000
```

4. Update the `templates/service.yaml` file:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "webapp.fullname" . }}
  labels:
    {{- include "webapp.labels" . | nindent 4 }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: {{ .Values.service.targetPort }}
      protocol: TCP
      name: http
  selector:
    {{- include "webapp.selectorLabels" . | nindent 4 }}
```

### Install the Helm Chart
```bash
helm install webapp ./webapp --namespace webapp
```

### Accessing the Deployment
Check the status of your pods and services:
```bash
kubectl get pods -n webapp
kubectl get svc -n webapp
```

Forward the service port to access it locally:
```bash
kubectl port-forward service/webapp -n webapp 5000:5000 --address=0.0.0.0 &
```

Open your browser and visit:
```bash
http://localhost:5000
```
![Screenshot (36)](https://github.com/user-attachments/assets/3612df48-aa1c-498a-9ccf-295c7ba8d7a9)


## Uninstall Helm and Delete Minikube
To uninstall Helm and delete Minikube:
```bash
helm uninstall webapp -n webapp
minikube delete
```

This concludes the deployment guide for Kubernetes on Minikube using Helm.

