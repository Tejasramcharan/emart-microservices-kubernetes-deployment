<div align="center">

# 🛒 Cartify — Microservices on Kubernetes

### Production-grade e-commerce backend | Microservices | Kubernetes | Observability

[![Node.js](https://img.shields.io/badge/API%20Gateway-Node.js%20%2F%20Express-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)](https://nodejs.org/)
[![Spring Boot](https://img.shields.io/badge/User%20Service-Spring%20Boot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![FastAPI](https://img.shields.io/badge/Cart%20Service-FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)](https://prometheus.io/)
[![Grafana](https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white)](https://grafana.com/)

> **Cartify** is a cloud-native e-commerce backend built on a polyglot microservices architecture, containerised with Docker and orchestrated on Kubernetes. Each service is independently deployable, language-agnostic, and observable via a full Prometheus + Grafana monitoring stack.

</div>

---

## 📐 Architecture

```
                          ┌─────────────────────┐
                          │   Client / Browser   │
                          └──────────┬──────────┘
                                     │ HTTPS
                          ┌──────────▼──────────┐
                          │  Ingress Controller  │
                          │   + Load Balancer    │  ← TLS termination, routing rules
                          └──────────┬──────────┘
                                     │
                    ╔════════════════▼════════════════════╗
                    ║         Kubernetes Cluster           ║
                    ║                                      ║
                    ║   ┌──────────────────────────────┐  ║
                    ║   │       API Gateway             │  ║
                    ║   │    Node.js / Express          │  ║
                    ║   │  Routing · Auth · Rate-limit  │  ║
                    ║   └──────┬────────────┬───────────┘  ║
                    ║          │            │               ║
                    ║  ┌───────▼──┐   ┌────▼──────────┐   ║
                    ║  │  User    │   │  Cart Service  │   ║
                    ║  │ Service  │   │ Python/FastAPI │   ║
                    ║  │  Java /  │   │  Add, remove,  │   ║
                    ║  │  Spring  │   │  view cart     │   ║
                    ║  └──────────┘   └───────────────┘   ║
                    ║                                      ║
                    ║   ┌────────────────────────────────┐ ║
                    ║   │  Prometheus  ──►  Grafana       │ ║
                    ║   │  Metrics scraping & dashboards  │ ║
                    ║   └────────────────────────────────┘ ║
                    ╚══════════════════════════════════════╝
```

---

## 🧩 Microservices Overview

| Service | Language / Framework | Responsibility | Port |
|---------|---------------------|----------------|------|
| **API Gateway** | Node.js / Express | Single entry point — request routing, auth middleware, rate limiting | `3000` |
| **User Service** | Java / Spring Boot | User registration, login, profile management, JWT issuance | `8080` |
| **Cart Service** | Python / FastAPI | Cart operations — add, update, remove items, fetch cart state | `8000` |

Each service is:
- Independently containerised with its own `Dockerfile`
- Deployed as a Kubernetes `Deployment` with configurable replicas
- Exposed internally via a `ClusterIP` Service
- Configured through Kubernetes `ConfigMaps` and `Secrets`

---

## 🗂️ Project Structure

```bash
cartify-microservices-kubernetes-deployment/
│
├── api-gateway/                  # Node.js / Express API Gateway
│   ├── src/
│   │   ├── index.js              # Entry point & route definitions
│   │   └── middleware/           # Auth, rate-limit, logging
│   ├── Dockerfile
│   └── package.json
│
├── user-service/                 # Java / Spring Boot User Service
│   ├── src/main/java/
│   │   └── com/cartify/user/     # Controllers, services, repositories
│   ├── Dockerfile
│   └── pom.xml
│
├── cart-service/                 # Python / FastAPI Cart Service
│   ├── app/
│   │   ├── main.py               # FastAPI app & route definitions
│   │   └── routers/              # Cart CRUD endpoints
│   ├── Dockerfile
│   └── requirements.txt
│
├── k8s/                          # Kubernetes manifests
│   ├── api-gateway/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── user-service/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── cart-service/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── ingress.yaml              # Ingress + Load Balancer config
│   └── monitoring/
│       ├── prometheus-config.yaml
│       └── grafana-deployment.yaml
│
└── docker-compose.yml            # Local development setup
```

---

## 🛠️ Tech Stack

### Services

| Layer | Technology | Why |
|-------|-----------|-----|
| API Gateway | Node.js / Express | Lightweight, non-blocking I/O ideal for routing and proxying |
| User Service | Java / Spring Boot | Type-safe, enterprise-grade; rich ecosystem for security & JPA |
| Cart Service | Python / FastAPI | Async-first, automatic OpenAPI docs, fast iteration speed |

### Infrastructure & Observability

| Tool | Role |
|------|------|
| Docker | Containerise each service with isolated runtimes |
| Kubernetes | Orchestrate, auto-heal, and scale services across nodes |
| Kubernetes Ingress | Route external HTTP/HTTPS traffic into the cluster |
| Load Balancer | Distribute traffic across healthy pods |
| Prometheus | Scrape `/metrics` endpoints from each service |
| Grafana | Visualise metrics — latency, error rate, pod health |
| ConfigMaps & Secrets | Decouple config and credentials from container images |

---

## 🚀 Getting Started

### Prerequisites

```bash
docker --version        # Docker 20+
kubectl version         # kubectl 1.26+
minikube version        # or any K8s cluster (EKS, GKE, etc.)
```

### 1. Clone the repository

```bash
git clone https://github.com/Tejasramcharan/cartify-microservices-kubernetes-deployment.git
cd cartify-microservices-kubernetes-deployment
```

### 2. Run locally with Docker Compose

```bash
docker-compose up --build
```

| Service | Local URL |
|---------|-----------|
| API Gateway | http://localhost:3000 |
| User Service | http://localhost:8080 |
| Cart Service (+ Swagger UI) | http://localhost:8000/docs |

### 3. Deploy to Kubernetes

```bash
# Start your cluster
minikube start
minikube addons enable ingress

# Deploy all services and monitoring
kubectl apply -f k8s/

# Verify pods are running
kubectl get pods -A

# Get Ingress address
kubectl get ingress
```

### 4. Deploy monitoring stack

```bash
kubectl apply -f k8s/monitoring/

# Access Grafana
minikube service grafana
```

---

## 📡 API Endpoints

All requests enter through the **API Gateway** at `http://<ingress-host>`.

| Method | Path | Routes to | Description |
|--------|------|-----------|-------------|
| `POST` | `/api/auth/register` | User Service | Register new user |
| `POST` | `/api/auth/login` | User Service | Login, receive JWT |
| `GET` | `/api/users/:id` | User Service | Get user profile |
| `GET` | `/api/cart` | Cart Service | Fetch current cart |
| `POST` | `/api/cart/add` | Cart Service | Add item to cart |
| `DELETE` | `/api/cart/remove/:itemId` | Cart Service | Remove item from cart |

FastAPI auto-generates interactive docs for the Cart Service at `/docs` (Swagger UI) and `/redoc`.

---

## 📊 Observability

Cartify ships with a production-grade observability stack.

**Prometheus** scrapes metrics from all pods on a configurable interval. Key metrics:

- `http_requests_total` — request count by service, method, and status code
- `http_request_duration_seconds` — latency histogram per endpoint
- `up` — liveness probe per pod

**Grafana** dashboards surface:

- Real-time request rate and error rate per microservice
- P50 / P95 / P99 latency breakdown
- Pod replica count and restart tracking
- CPU and memory usage per Deployment

```bash
# Port-forward Grafana
kubectl port-forward svc/grafana 3001:3000

# Port-forward Prometheus
kubectl port-forward svc/prometheus 9090:9090
```

---

## ☸️ Kubernetes Concepts Used

| Concept | Usage in Cartify |
|---------|-----------------|
| `Deployment` | Manages replicas and rolling updates for each service |
| `Service (ClusterIP)` | Internal service discovery between microservices |
| `Ingress` | Routes external traffic to the API Gateway |
| `ConfigMap` | Non-sensitive configuration (service URLs, ports) |
| `Secret` | Sensitive data (DB passwords, JWT secret key) |
| `Namespace` | Logical isolation between app and monitoring workloads |

---

## 🧪 Running Tests

```bash
# User Service — Maven
cd user-service && mvn test

# Cart Service — Pytest
cd cart-service && pip install -r requirements.txt && pytest

# API Gateway — Jest
cd api-gateway && npm install && npm test
```

---

## 🔮 Future Enhancements

- [ ] Order Service and Payment Service
- [ ] Async event bus with RabbitMQ or Kafka
- [ ] Distributed tracing with OpenTelemetry + Jaeger
- [ ] Redis caching layer for cart sessions
- [ ] Full CI/CD pipeline with GitHub Actions + ArgoCD GitOps

---

## 👤 Author

**Tejas Ramcharan**

[![GitHub](https://img.shields.io/badge/GitHub-Tejasramcharan-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/Tejasramcharan)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/tejasramcharan)

---

<div align="center">

*Built with a polyglot microservices mindset — right tool, right job, every service.*

</div>
