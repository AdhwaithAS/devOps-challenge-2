### **Kubernetes Challenge Submission**

This submission includes the containerization and deployment of the provided Flask app using Docker and Kubernetes (Minikube).

**Details:**

* **DockerHub Image:** [https://hub.docker.com/repository/docker/adhwaithas/flask-app](https://hub.docker.com/repository/docker/adhwaithas/flask-app)
* **Deployment YAML:** `deployment.yaml` included in this submission
* **Service YAML:** `service.yaml` included in this submission
* **Screenshots:**

  * Pods running: `screenshots/pods-running.png`
       ![Pods Running](https://github.com/AdhwaithAS/devOps-challenge-2/blob/main/submissions/AdhwaithAS/screenshot/pods-running.png)
  
  * Flask app accessible in browser via NodePort: `screenshots/app-access.png`
       ![Flask app accessible in browser via NodePort](https://github.com/AdhwaithAS/devOps-challenge-2/blob/main/submissions/AdhwaithAS/screenshot/app-access.png)

  * Minikube Flask Service: `screenshots/service_view.png`
       ![Minikube Flask Service](https://github.com/AdhwaithAS/devOps-challenge-2/blob/main/submissions/AdhwaithAS/screenshot/service_view.png)

**Folder Structure in this Submission:**

```
submissions/AdhwaithAS/
├─ service.yaml
├─ deployment.yaml
├─ requirements.txt
├─ app.py
├─ .dockerignore
├─ Dockerfile
├─ screenshots/
   ├─ pods-running.png
   └─ app-access.png
   └─ service_view.png
```

All tasks, including deploying 2 replicas of the Flask app, exposing the service via NodePort, and verifying accessibility through Minikube, have been completed as per the challenge instructions.

---
