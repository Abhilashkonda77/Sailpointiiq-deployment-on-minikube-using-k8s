# Dockerization and Kubernetes Deployment of SailPoint IdentityIQ 8.4

> **Disclaimer:** SailPoint IdentityIQ is proprietary software and requires a valid license from SailPoint. This project is for educational and evaluation purposes only and must not be used in production.

---

## 📦 Project Folder Structure

```
Customize/docker-sailpoint-identityiq/
├── app
│   ├── binaries
│   │   ├── base
│   │   │   └── identityiq-8.4.zip
│   │   ├── efixes
│   │   └── patch
│   ├── Dockerfile
│   ├── scripts
│   │   └── entrypoint.sh
│   └── tomcat
│       ├── conf
│       │   ├── Catalina
│       │   │   └── localhost
│       │   │       └── manager.xml
│       │   └── tomcat-users.xml
│       └── webapps
│           └── ROOT
│               └── index.html
├── k8s
│   ├── adminer-deployment.yaml
│   ├── adminer-service.yaml
│   ├── app-deployment.yaml
│   ├── app-service.yaml
│   ├── db-deployment.yaml
│   ├── db-service.yaml
│   └── env-configmap.yaml
├── minikube-linux-amd64
└── README.md
```

---

## 🛠 Requirements

* A valid SailPoint IdentityIQ license
* IdentityIQ 8.4 binaries (base, patch, efixes)
* Docker and Minikube

---

## ❓ Why Use This

This setup was built to allow quick evaluation and experimentation with SailPoint IdentityIQ in a local Kubernetes (Minikube) environment. It is not intended for production use. Use this to learn, test configurations, and validate new versions.

---

## 🚀 How to Use

### 1. Preparation

* Drop the IdentityIQ binaries into the appropriate folder:

  * `identityiq-8.4.zip` → `app/binaries/base/`
  * Patch jar → `app/binaries/patch/`
  * Efix zip files → `app/binaries/efixes/`

### 2. Build the Docker Image

```bash
eval $(minikube docker-env)
cd app/
docker build -t docker-sailpoint-identityiq-app .
```

### 3. Create Namespace (Optional but Recommended)

```bash
kubectl create namespace sailpointiiq-dev
```

### 4. Apply Kubernetes Resources

```bash
kubectl apply -n sailpointiiq-dev -f k8s/env-configmap.yaml
kubectl apply -n sailpointiiq-dev -f k8s/db-deployment.yaml
kubectl apply -n sailpointiiq-dev -f k8s/db-service.yaml
kubectl apply -n sailpointiiq-dev -f k8s/app-deployment.yaml
kubectl apply -n sailpointiiq-dev -f k8s/app-service.yaml
kubectl apply -n sailpointiiq-dev -f k8s/adminer-deployment.yaml
kubectl apply -n sailpointiiq-dev -f k8s/adminer-service.yaml
```

### 5. Access the Application

#### Option A: Port Forwarding (Recommended for Local Testing)

```bash
kubectl port-forward -n sailpointiiq-dev deployment/identityiq 8080:8080
```

Then access:

```
http://localhost:8080/identityiq
```

#### Option B: NodePort

```bash
minikube ip
kubectl get svc -n sailpointiiq-dev
# Combine IP and NodePort:
http://<minikube-ip>:<nodePort>
```

---

## 🔁 Fresh Start Between Versions

If switching between IdentityIQ versions or you want to reset:

```bash
docker system prune -a
kubectl delete namespace sailpointiiq-dev
```

---

## 🔐 Access Credentials (For Tomcat and Adminer)

* Tomcat Manager: [http://localhost:8080/manager/](http://localhost:8080/manager/)

  * Username: `admin`
  * Password: `admin`
* Adminer: [http://localhost:8081/](http://localhost:8081/)

---

## 🔧 Troubleshooting

* Check logs:

```bash
kubectl logs -n sailpointiiq-dev <pod-name>
```

* Confirm DB resolution:

```bash
kubectl exec -n sailpointiiq-dev <app-pod> -- ping db
```

---

## 🙅‍♂️ Disclaimer

This configuration is for educational use only. SailPoint IdentityIQ is a proprietary product. Never push Docker images with this software to public registries.

For actual application usage, licensing, or support, please contact SailPoint directly.

---

## 📩 Support

This is a personal educational project. If you encounter issues with this configuration, feel free to open an issue or PR with suggestions.

