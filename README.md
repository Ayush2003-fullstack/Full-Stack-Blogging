## 🔧 **Overview: DevSecOps CI/CD Architecture**

This architecture enables **secure and automated code delivery** from source to production using:

* CI/CD (Jenkins + GitHub + Maven)
* Security (Trivy + SonarQube)
* IaC (Terraform)
* Containerization (Docker + Kubernetes)
* Monitoring (Prometheus + Grafana)
* Domain configuration (GoDaddy)

---

## 🧑‍💻 1. Developer → GitHub

### ✔️ Role:

The developer writes code and pushes it to a **GitHub repository**.

### ✔️ Setup:

```bash
git init
git remote add origin https://github.com/your-repo/project.git
git add .
git commit -m "initial commit"
git push origin main
```

---

## 🔁 2. GitHub → Jenkins (CI/CD Trigger)

### ✔️ Role:

Jenkins polls GitHub or listens for **webhooks** to trigger a build.

### ✔️ Jenkins Setup:

```bash
docker run -d -p 8080:8080 -p 50000:50000 --name jenkins jenkins/jenkins:lts
```

### ✔️ Ports:

* `8080`: Jenkins Web UI
* `50000`: Jenkins agent port

---

## 🧱 3. Jenkins Pipeline

### ✔️ Pipeline Flow:

1. Checkout code from GitHub
2. Run static code analysis
3. Perform security scans with Trivy
4. Quality analysis using SonarQube
5. Compile and test with Maven
6. Build & scan Docker image
7. Deploy to Kubernetes

---

## 🔍 4. Trivy - Security Scanning

### ✔️ Role:

Scans for vulnerabilities in:

* Filesystem (`fs`)
* Docker images (`image`)

### ✔️ Setup:

```bash
brew install aquasecurity/trivy/trivy
trivy fs .
trivy image my-app-image
```

---

## 📊 5. SonarQube - Code Quality Analysis

### ✔️ Role:

Performs **static code analysis** (bugs, code smells, vulnerabilities).

### ✔️ Setup:

```bash
docker run -d --name sonarqube -p 9000:9000 sonarqube
```

### ✔️ Port:

* `9000`: SonarQube UI

---

## 🧪 6. Maven - Build and Dependency Management

### ✔️ Role:

Builds and tests Java code.

### ✔️ Jenkinsfile Step:

```groovy
sh 'mvn clean install'
```

---

## 🐳 7. Docker - Containerization

### ✔️ Role:

Packages application into containers.

### ✔️ Build & Push Commands:

```bash
docker build -t your-image-name .
docker tag your-image-name your-dockerhub/image:tag
docker push your-dockerhub/image:tag
```

### ✔️ Port (custom app-dependent):

* Usually `3000`, `8080`, or app-specific.

---

## 🔒 8. Trivy (Again) - Docker Image Security Scan

### ✔️ Command:

```bash
trivy image your-image-name
```

---

## ☸️ 9. Kubernetes - Container Orchestration

### ✔️ Role:

Deploys and manages containers at scale.

### ✔️ Setup (using minikube or EKS):

```bash
kubectl apply -f deployment.yaml
kubectl get svc
```

---

## ⚙️ 10. Terraform - Infrastructure as Code

### ✔️ Role:

Automates K8s infrastructure provisioning (EKS, VPC, IAM, etc.)

### ✔️ Commands:

```bash
terraform init
terraform plan
terraform apply
```

---

## 🌐 11. GoDaddy - Domain Management

### ✔️ Role:

Maps domain to Kubernetes LoadBalancer URL.

### ✔️ Steps:

* Copy the LoadBalancer DNS (e.g., `abcd.elb.amazonaws.com`)
* Go to GoDaddy DNS → Add CNAME or A Record:

  ```
  Type: A or CNAME
  Name: www
  Value: <LoadBalancer DNS>
  ```

---

## 📈 12. Prometheus - Monitoring

### ✔️ Role:

Collects metrics from K8s nodes and pods.

### ✔️ Setup:

```bash
docker run -d -p 9090:9090 prom/prometheus
```

### ✔️ Port:

* `9090`: Prometheus Web UI

---

## 📉 13. Grafana - Visualization

### ✔️ Role:

Visualizes Prometheus metrics with dashboards.

### ✔️ Setup:

```bash
docker run -d -p 3000:3000 grafana/grafana
```

### ✔️ Port:

* `3000`: Grafana Dashboard

---

## 🔁 Pipeline Flow Summary:

1. 👨‍💻 Dev pushes code to GitHub
2. 🔁 GitHub webhook triggers Jenkins
3. 🛠 Jenkins builds code with Maven
4. 🔍 Trivy + SonarQube for security & quality
5. 🐳 Docker builds and pushes container
6. ☸️ Deploys on Kubernetes via Terraform
7. 🌐 GoDaddy links domain to app
8. 📊 Prometheus collects metrics
9. 📈 Grafana visualizes metrics

---

## 🚀 Bonus: Kubernetes Exposure Port

If using NodePort:

```yaml
ports:
  - name: http
    port: 80
    targetPort: 8080
    nodePort: 30080
type: NodePort
```

If using LoadBalancer:

```yaml
type: LoadBalancer
ports:
  - port: 80
    targetPort: 8080
```

---


