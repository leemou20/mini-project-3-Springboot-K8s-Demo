# Springboot Kubernetes Demo Project

## Project Overview
This project demonstrates deploying a Spring Boot application on Kubernetes using Docker and Minikube. The application exposes a REST endpoint `/message` to confirm successful deployment.

**Project Name:** Springboot-K8s-Demo  
**Description:** A simple Spring Boot REST API deployed to Kubernetes with Docker image. The project covers building, packaging, Dockerizing, and deploying to a local Kubernetes cluster (Minikube) with NodePort service exposure.

---

## Architecture Diagram
```
+----------------+       +----------------+       +----------------+
|  Spring Boot   |       |  Docker Image  |       | Kubernetes Pod |
|  REST API      | ----> | springboot-    | ----> |  running app   |
+----------------+       | k8s-demo:1.0   |       +----------------+
                          +----------------+
                                  |
                                  v
                          NodePort Service
                                  |
                                  v
                        Access via http://<minikube-ip>:30080/message
```

---

## Prerequisites
- Java 21 installed
- Maven installed
- git installed
- Docker installed
- Minikube installed
- Kubectl installed

---

## Step-by-Step Instructions

### 1. Setup Environment
1. Install Java:
```bash
sudo apt update
sudo apt install openjdk-21-jdk -y
java -version
```
2. Install Maven:
```bash
sudo apt install maven -y
mvn -version
```
3. Install Git
```bash
sudo apt install git -y
git --version
```
4. Install Docker:
```bash
sudo apt install docker.io -y
docker --version
```
5. Install Minikube:
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
minikube version
```
6. Install Kubectl:
```bash
sudo snap install kubectl --classic
kubectl version --client
```

### 2. Clone the project repository from GitHub:
```bash
cd ~
git clone https://github.com/leemou20/mini-project-3-Springboot-K8s-Demo.git
cd mini-project-3-Springboot-K8s-Demo

---

### 3. Build Spring Boot Application
```bash
mvn clean package -DskipTests
```
> The jar file is generated in `target/springboot-k8s-demo.jar`

---

### 4. Start Minikube and Configure Docker
```bash
minikube start --driver=docker
eval $(minikube docker-env)
```

---

### 5. Build Docker Image
```bash
docker build -t springboot-k8s-demo:1.0 .
```
> Ensure your Dockerfile uses Java 21 JRE:
```dockerfile
FROM eclipse-temurin:21-jre-jammy
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","app.jar"]
```

---

### 6. Deploy to Kubernetes
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get pods
kubectl get svc
```
> Deployment YAML should match:
```yaml
image: springboot-k8s-demo:1.0
imagePullPolicy: IfNotPresent
```

---

### 7. Verify Deployment
```bash
kubectl logs <pod-name>
curl http://$(minikube ip):30080/message
```
> Expected output:
```
Congratulation we have successfully deployed your application to kubernetes !!
```

---

### 8. Access via Browser
```bash
minikube service springboot-k8s-service
```
> This opens your default browser with the NodePort service.

---
### 9. clean up kubernetes and minikube resources

After finishing your work, it’s good practice to delete all deployments, services, and optionally stop Minikube to free resources:

# Delete Kubernetes deployment and service
```bash
kubectl delete deployment springboot-k8s-deployment
kubectl delete service springboot-k8s-service
```

# Verify all pods and services are removed
```bash
kubectl get pods
kubectl get svc
```

# (Optional) Stop and delete Minikube cluster for a fresh start next time
```bash
minikube stop
minikube delete
```

# (Optional) Remove Docker images from Minikube to free space
```bash
eval $(minikube docker-env)
docker rmi springboot-k8s-demo:1.0
```

✅ This ensures your Kubernetes environment is fully cleaned up and ready for the next project run.
 
 Here’s a single-line command that safely deletes your deployment, service, pods, and optionally cleans up Minikube and Docker images
```bash
kubectl delete deployment springboot-k8s-deployment service springboot-k8s-service && kubectl delete pod -l app=spring-boot-k8s --ignore-not-found && eval $(minikube docker-env) && docker rmi springboot-k8s-demo:1.0 --force && minikube stop && minikube delete
```
---
## Kubernetes YAML Explanation

### Deployment.yaml
- **replicas:** 2 pods  
- **selector:** label to match pods  
- **containers:** image, ports, imagePullPolicy  

### Service.yaml
- **type NodePort:** exposes service externally  
- **selector:** matches pod labels  
- **ports:** container port 8080, NodePort 30080  

---

## Interview Tips
- Explain the flow: **Code → Jar → Docker → Kubernetes → NodePort → Browser/curl**  
- Explain **CrashLoopBackOff**: usually caused by **Java version mismatch** or image errors  
- Explain **why NodePort is used** for external access  
- Explain **rolling updates** using `kubectl rollout restart deployment`  
- Mention **minikube service** command for testing from host  

---

## Bonus Tips
- Use `/hello` or `/message` endpoints to test controller  
- Use `kubectl exec -it <pod-name> -- sh` to debug pod content  
- Use `kubectl logs <pod-name>` for debugging  

---

## Folder Structure

```
springboot-k8s-demo/
│
├── Dockerfile
├── deployment.yaml
├── service.yaml
├── pom.xml
├── src/              
│   └── main/java/com/javatechie/k8s/
│       ├── SpringbootK8sDemoApplication.java
├── README.md         
```
