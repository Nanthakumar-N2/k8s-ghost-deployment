# Kubernetes Ghost Blog Deployment

[![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](https://www.docker.com/)
[![Ghost](https://img.shields.io/badge/Ghost-738A94?style=flat&logo=ghost&logoColor=white)](https://ghost.org/)

## 🚀 Live Demo
🔗 **[Live Application](https://unaccidental-aniyah-germfree.ngrok-free.dev/)**



---

## 📋 Project Overview

This project demonstrates a complete Kubernetes deployment of Ghost CMS (Content Management System) on a local Minikube cluster with public internet access via Ngrok tunnel.

### Why Ghost?
- **Popular**: 45,000+ stars on GitHub
- **Production-Ready**: Used by major publications
- **Modern**: Built on Node.js
- **Open Source**: MIT licensed

---

## 🏗️ Architecture

\`\`\`
Internet → Ngrok Tunnel → Minikube Service → Ghost Pod(s) → SQLite Database
\`\`\`

### Components:
- **Kubernetes Cluster**: Minikube (local development cluster)
- **Container Runtime**: Docker
- **Application**: Ghost 5 Alpine (lightweight Linux image)
- **Database**: SQLite (embedded, file-based)
- **Service Type**: NodePort (exposes on port 30082)
- **Public Access**: Ngrok secure tunnel
- **Storage**: Kubernetes emptyDir volume (persistent during pod lifecycle)

---

## 📁 Project Structure

\`\`\`
k8s-demo/
├── ghost-fixed.yaml          # Kubernetes manifests
├── README.md                  # This file
└── screenshots/              # Deployment evidence
    ├── kubectl-get-all.png
    ├── kubectl-pods.png
    ├── deployment-details.png
    ├── ghost-live.png
    └── ngrok-dashboard.png
\`\`\`

---

## 🛠️ Technologies Used

| Technology | Version | Purpose |
|------------|---------|---------|
| Kubernetes | 1.28+ | Container orchestration |
| Docker | 24.0+ | Container runtime |
| Minikube | 1.32+ | Local K8s cluster |
| Ghost | 5.x | Blogging platform |
| SQLite | 3.x | Database |
| Ngrok | 3.x | Public tunneling |

---

## 📦 Deployment Configuration

### Kubernetes Resources

#### Deployment Specs:
- **Replicas**: 1 (can be scaled)
- **Image**: \`ghost:5-alpine\`
- **Container Port**: 2368
- **Resource Limits**:
  - CPU: 200m (request) - 500m (limit)
  - Memory: 256Mi (request) - 512Mi (limit)

#### Service Specs:
- **Type**: NodePort
- **Port**: 2368 (internal)
- **NodePort**: 30082 (external)
- **Selector**: app=ghost

#### Environment Variables:
- \`NODE_ENV=development\`
- \`url=http://localhost:2368\`
- \`database__client=sqlite3\`
- \`database__connection__filename=/var/lib/ghost/content/data/ghost.db\`

---

## 🚀 Deployment Steps

### Prerequisites
- Docker Desktop installed and running
- Minikube installed
- kubectl installed
- Ngrok account (free tier)

### Step 1: Start Minikube
\`\`\`powershell
minikube start --driver=docker
\`\`\`

### Step 2: Deploy Ghost
\`\`\`powershell
kubectl apply -f ghost-fixed.yaml
\`\`\`

### Step 3: Verify Deployment
\`\`\`powershell
# Check pods
kubectl get pods

# Check services
kubectl get svc

# View all resources
kubectl get all
\`\`\`

### Step 4: Access Locally
\`\`\`powershell
# Option 1: Using minikube service
minikube service ghost-service

# Option 2: Using port-forward
kubectl port-forward service/ghost-service 8080:2368
# Then open: http://localhost:8080
\`\`\`

### Step 5: Expose Publicly (Ngrok)
\`\`\`powershell
# Get minikube IP
minikube ip

# Start ngrok tunnel
ngrok http http://<MINIKUBE-IP>:30082
\`\`\`

---

## 🔧 Kubernetes Commands Reference

### Pod Management
\`\`\`powershell
# List all pods
kubectl get pods

# Get detailed pod info
kubectl get pods -o wide

# Describe specific pod
kubectl describe pod <POD-NAME>

# View pod logs
kubectl logs <POD-NAME>

# Follow logs in real-time
kubectl logs -f <POD-NAME>

# Execute command in pod
kubectl exec -it <POD-NAME> -- /bin/sh
\`\`\`

### Deployment Management
\`\`\`powershell
# List deployments
kubectl get deployments

# Describe deployment
kubectl describe deployment ghost-blog

# Scale deployment
kubectl scale deployment ghost-blog --replicas=3

# Update image
kubectl set image deployment/ghost-blog ghost=ghost:latest

# Rollback deployment
kubectl rollout undo deployment/ghost-blog

# Check rollout status
kubectl rollout status deployment/ghost-blog
\`\`\`

### Service Management
\`\`\`powershell
# List services
kubectl get svc

# Describe service
kubectl describe svc ghost-service

# Get service URL (Minikube)
minikube service ghost-service --url
\`\`\`

### Debugging
\`\`\`powershell
# Get events
kubectl get events --sort-by=.metadata.creationTimestamp

# Get resource usage
kubectl top pods

# Get cluster info
kubectl cluster-info

# View all resources
kubectl get all -A
\`\`\`

### Cleanup
\`\`\`powershell
# Delete deployment and service
kubectl delete -f ghost-fixed.yaml

# Or delete by name
kubectl delete deployment ghost-blog
kubectl delete service ghost-service

# Stop minikube
minikube stop

# Delete minikube cluster
minikube delete
\`\`\`

---

## 🐛 Troubleshooting

### Pod Not Starting
\`\`\`powershell
# Check pod status
kubectl describe pod <POD-NAME>

# Check logs
kubectl logs <POD-NAME>

# Common fixes:
# 1. Resource constraints
kubectl describe node

# 2. Image pull errors
kubectl get events
\`\`\`

### Service Not Accessible
\`\`\`powershell
# Verify service exists
kubectl get svc

# Check endpoints
kubectl get endpoints

# Test with port-forward
kubectl port-forward service/ghost-service 8080:2368
\`\`\`

### Minikube Issues
\`\`\`powershell
# Restart minikube
minikube stop
minikube start

# Delete and recreate
minikube delete
minikube start --driver=docker --memory=4096
\`\`\`

---

## 📊 Performance Considerations

### Resource Optimization
- **CPU**: Ghost is Node.js based, benefits from multi-core
- **Memory**: 256Mi minimum, 512Mi recommended for production
- **Storage**: SQLite grows with content, monitor disk usage

### Scaling Strategy
\`\`\`powershell
# Horizontal scaling (multiple pods)
kubectl scale deployment ghost-blog --replicas=3

# Note: SQLite is single-file, not ideal for multi-pod
# Production: Use MySQL/PostgreSQL with persistent volume
\`\`\`

---

## 🔒 Security Considerations

### Current Setup (Development)
- ✅ Resource limits prevent resource exhaustion
- ✅ SQLite database isolated per pod
- ⚠️ No TLS/SSL (handled by Ngrok in free tier)
- ⚠️ No authentication on K8s API
- ⚠️ No network policies

### Production Recommendations
- Use secrets for sensitive data
- Implement network policies
- Use TLS certificates
- Enable RBAC
- Use managed database (not SQLite)
- Implement backup strategy
- Use persistent volumes (not emptyDir)

---

## 📈 Monitoring & Observability

### View Logs
\`\`\`powershell
# Real-time logs
kubectl logs -f deployment/ghost-blog

# Last 100 lines
kubectl logs deployment/ghost-blog --tail=100

# Logs from all pods
kubectl logs -l app=ghost
\`\`\`

### Resource Usage
\`\`\`powershell
# Enable metrics server
minikube addons enable metrics-server

# View resource usage
kubectl top pods
kubectl top nodes
\`\`\`

---

## 🎯 Learning Outcomes

This project demonstrates proficiency in:

1. **Container Orchestration**
   - Writing Kubernetes manifests
   - Understanding Deployments, Services, Volumes
   - Resource management

2. **Troubleshooting**
   - Debugging failed pods
   - Analyzing logs
   - Fixing configuration issues

3. **Networking**
   - Service types (NodePort, ClusterIP)
   - Port forwarding
   - External access (Ngrok)

4. **DevOps Practices**
   - Infrastructure as Code
   - Version control
   - Documentation
   - Reproducible deployments

---

## 📚 Additional Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Ghost Documentation](https://ghost.org/docs/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
- [Docker Documentation](https://docs.docker.com/)
- [Ngrok Documentation](https://ngrok.com/docs)

---

## 🤝 Contributing

This is a learning/demo project. Feel free to:
- Fork and experiment
- Suggest improvements
- Report issues
- Share feedback

---

## 📝 License

This project configuration is open source. Ghost CMS is licensed under MIT.

---

## 👤 Author

**[Nanthakumar]**
- GitHub: [@Nanthakumar-N2](https://github.com/Nanthakumar-N2)
- LinkedIn: [Nanthakumar](https://www.linkedin.com/in/nantha-kumar-2bb760267/e)
- Email: dev.nanthakumar@gmail.com

---

## 🙏 Acknowledgments

- Ghost Foundation for the amazing CMS
- Kubernetes community
- Minikube developers
- Ngrok for free tunneling service

---

## 📅 Project Timeline

- **Created**: November 12, 2025
- **Status**: Active / Demo
- **Purpose**: DevOps Skills Demonstration

---

## 🔄 Version History

### v1.0.0 (2025-11-12)
- Initial deployment
- Ghost 5 Alpine
- SQLite database
- NodePort service
- Ngrok tunnel integration
- Complete documentation

---

**⭐ If this helped you, please star this repo!**
