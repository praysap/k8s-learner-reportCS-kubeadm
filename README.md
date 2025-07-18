# 🚀 Kubernetes MERN Stack Deployment using Helm & Jenkins

This repository contains a complete Kubernetes deployment setup for a MERN (MongoDB, Express.js, React, Node.js) application using:
- 🧠 **Kubernetes** for container orchestration
- 📦 **Helm** for templated, reusable Kubernetes manifests
- 🐳 **Docker** for containerizing the applications
- ⚙️ **Jenkins** (optional) for CI/CD automation
---

## 📁 Project Structure
├── k8s/
├── learnerReportCS_backend/           # Backend K8s manifests
├── learnerReportCS_frontend/          # Frontend K8s manifests
├── mern-chart/                        # Helm chart for the MERN stack
│   ├── templates/
│   │   ├── backend.yaml
│   │   ├── frontend.yaml
│   │   ├── mongo.yaml
│   │   ├── secrets.yaml               ✅ Secret manifest added here
│   ├── Chart.yaml
│   └── values.yaml
├── jenkinsfile
└── README.md
          
- You need to clone backend & frontend repos inside project directory if you want it to work locally.
---



###  Build the Docker image (frontend)
```bash
docker image build --no-cache --build-arg REACT_APP_API_BASE_URL=http://10.228.12.107:30585 -t praysap/learner-frontend:latest .
```
###  Push the image to Docker Hub
```bash
docker push praysap/learner-frontend:latest
```
<img width="944" height="441" alt="image" src="https://github.com/user-attachments/assets/ef1191ce-0114-461f-a9bf-e604704851ef" />


###  Build the Docker image (backend)
```bash
docker build -t praysap/learner-backend:latest .
```
###  Push the image to Docker Hub
```bash
docker push praysap/learner-backend:latest
```
<img width="944" height="434" alt="image" src="https://github.com/user-attachments/assets/ea646965-0d78-47ac-9080-3c044fe3ef77" />
<img width="941" height="177" alt="image" src="https://github.com/user-attachments/assets/ecb46d95-6905-4f39-8f0c-846770bbfe25" />


 ### 📥 Install Helm CLI on Linux/mascOS
```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```
### 📥 Install Helm on On Windows (via Chocolatey):
```bash
choco install kubernetes-helm
```
```bash
helm version
```
---
### 🛠 Create Helm Chart
```bash
helm create mern-app
```
### 🚀 Deployment with Helm
```bash
cd mern-app dir
```
```bash
helm upgrade --install mern-app . --namespace mern --create-namespace
```
---
### ✅ Get running pods
```bash
kubectl get pods -n mern
```
<img width="939" height="450" alt="image" src="https://github.com/user-attachments/assets/e6ea82e5-7c00-41ef-af29-d77eb47b2374" />

---
### ✅ Verify Deployment
```bash
kubectl get all -n mern
```
- Make sure all pods are 1/1 READY and services are running (frontend-service, backend-service, mongo).
<img width="939" height="450" alt="image" src="https://github.com/user-attachments/assets/0b610ea9-9fa1-4b86-9e39-87bd0c1d027f" />
  
---
### 🔁 Access a running pod (e.g. frontend)
```bash
kubectl exec -it frontend-deployment-67f9fbc9f6-pt6wq -n mern -- /bin/sh
```
 <img width="939" height="450" alt="image" src="https://github.com/user-attachments/assets/69ecd42c-f198-4f1f-9d7c-9cfc71f7a2c6" />

---
### 🧪 Delete All Resources in Namespace
```bash
kubectl delete all --all -n mern
```
- This deletes:Pods,Services,Deployments,ReplicaSets and Any other workload in that namespace
---

#### Minikube [local]

<img width="939" height="238" alt="image" src="https://github.com/user-attachments/assets/7a4b26e1-69f7-47ad-a970-b5f6e741252d" />

#### Minikube [EC-2/Ubuntu]

<img width="939" height="254" alt="image" src="https://github.com/user-attachments/assets/6e02e259-0119-43c0-88d2-e2430cba0f1b" />


## 🌐 Access the App 
### Test via Port Forwarding [Local]
```bash
kubectl port-forward svc/frontend-service 8080:80 -n mern
kubectl port-forward svc/backend-service 3000:3000 -n mern
kubectl port-forward svc/mongo 28017:27017 -n mern
```
Frontend App available at: http://10.228.12.107:31063<br>

<img width="939" height="450" alt="image" src="https://github.com/user-attachments/assets/b99eabd2-45f7-47fa-b0bc-78533f55a6df" />
<img width="939" height="450" alt="image" src="https://github.com/user-attachments/assets/1d4cdccb-de77-482f-91f4-c692f5e98694" />
<img width="939" height="450" alt="image" src="https://github.com/user-attachments/assets/7262e7b8-9d4c-4b94-8f44-73686840677a" />

Backend App available at: http://10.228.12.107:30585<br>

<img width="939" height="450" alt="image" src="https://github.com/user-attachments/assets/f391ee13-f16d-48e8-a147-652a92828c19" />


Mongo DB available at: mongodb://localhost:27017<br>

### Test via Port Forwarding [Ec-2/Ubuntu]
```bash
kubectl port-forward svc/frontend-service -n mern 8080:80 --address=0.0.0.0
kubectl port-forward svc/backend-service -n mern 3000:3000 --address=0.0.0.0
kubectl port-forward svc/mongo 28017:27017 -n mern
```

Mongo DB available at: mongodb://public-ip:28017<br>

### Test via Minikube (OPTIONAL)
```bash
minikube service frontend-service -n mern
```
- This will open your browser or give you a URL — check the site.
---

## Configuration
### .env file in Backend
```bash
ATLAS_URI=mongodb://mongo:27017/blog_mern
```
### .env file in Frontend
Based on your setup you need to update.
```bash
REACT_APP_API_BASE_URL=http://backend-service:3000
OR
REACT_APP_API_BASE_URL=http://localhost:3000
```
---

## ⚙️ Jenkins configuration
### Pipeline setup
1. Create Jenkinsfile inside your project directory.
2. Create dockerhub credentials in jenkins.
3. Create jenkins pipeline.

```
pipeline {
    agent any
    
    environment {
        REPO_URL = 'https://github.com/vipulsaw/Container-Orchestration-1.git'
        REPO_NAME = 'Container-Orchestration-1'
        HELM_CHART_DIR = 'mern-chart'
        NAMESPACE = 'mern'
        RELEASE_NAME = 'mern-app'
        EC2_INSTANCE_IP = '54.145.162.64'
    }
    
    stages {
        stage('Checkout Source Code') {
            steps {
                checkout([$class: 'GitSCM', 
                         branches: [[name: '*/main']], 
                         userRemoteConfigs: [[url: env.REPO_URL]]])
                echo "Repository cloned successfully"
            }
        }
        
        stage('Copy Repository to Target EC2') {
            steps {
                sshagent(credentials: ['vipul']) {
                    script {
                        // Create directory on remote if it doesn't exist
                        sh """
                            ssh -o StrictHostKeyChecking=no ubuntu@${env.EC2_INSTANCE_IP} \
                            'mkdir -p ~/${env.REPO_NAME}'
                        """
                        
                        // Copy files using rsync
                        sh """
                            rsync -avz -e "ssh -o StrictHostKeyChecking=no" \
                            --exclude='.git' \
                            --delete \
                            ./ ubuntu@${env.EC2_INSTANCE_IP}:~/${env.REPO_NAME}/
                        """
                        
                        echo "Files copied to EC2 instance successfully"
                    }
                }
            }
        }
        
        stage('Install/Upgrade Helm Release') {
            steps {
                sshagent(credentials: ['vipul']) {
                    script {
                        // Run helm upgrade
                        sh """
                            ssh -o StrictHostKeyChecking=no ubuntu@${env.EC2_INSTANCE_IP} \
                            'cd ~/${env.REPO_NAME}/${env.HELM_CHART_DIR} && \
                            helm upgrade --install ${env.RELEASE_NAME} . \
                            --namespace ${env.NAMESPACE} \
                            --create-namespace'
                        """
                        
                        echo "Helm release upgraded successfully"
                    }
                }
            }
        }
        
        stage('Verify Deployment') {
            steps {
                sshagent(credentials: ['vipul']) {
                    script {
                        sh """
                            ssh -o StrictHostKeyChecking=no ubuntu@${env.EC2_INSTANCE_IP} \
                            'helm list -n ${env.NAMESPACE} && \
                            kubectl get pods -n ${env.NAMESPACE}'
                        """
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed! Check the logs for details.'
        }
    }
}

```

<img width="959" height="440" alt="image" src="https://github.com/user-attachments/assets/c82945fa-2d64-45ce-8548-69a006d47d03" />


### Validation commands
Compare deploy version of helm
```bash
helm history chart_name -n namespace_name
```
EXAMPLE 
```bash
helm history mern-chart -n mern
```
---

