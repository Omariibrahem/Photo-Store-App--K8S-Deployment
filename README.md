# Photo Store App – Kubernetes Deployment

A self-contained, end-to-end blueprint for deploying the Photo Store microservice on Kubernetes. This repo demonstrates:

- Ingress via Nginx and Istio Gateway
- Nginx as a LoadBalancer
- Core photo-app Deployment and Service  
- Optionally modularized with Helm 
---

## Architecture Diagram

```txt
                          ┌────────────────────┐
                          │    User Client     │
                          └────────┬───────────┘
                                   │ HTTPS
                                   ▼
                          ┌────────────────────┐
                          │  NGINX IngressCtrl │
                          └────────┬───────────┘
                                   │
                                   ▼
                          ┌────────────────────┐
                          │   Istio Gateway    │
                          │   (VirtualService) │
                          └────────┬───────────┘
                                   │
                                   ▼
                          ┌────────────────────┐
                          │     NGINX SVC      │
                          └────────┬───────────┘
                                   │
                                   ▼
                          ┌────────────────────┐
                          │     NGINX LB       │
                          └────────┬───────────┘
                                   │
                                   ▼
                          ┌────────────────────┐
                          │  photoapp-svc      │
                          │   (ClusterIP)      │
                          └────────┬───────────┘
                                   │
                                   ▼
                          ┌────────────────────┐
                          │   photoapp Pod(s)  │
                          │   (Deployment)     │
                          └────────────────────┘

                                                

---

## Service Flow

1. Client issues HTTPS request (eg postman)
2. Hits NGINX Ingress Controller  
3. Routed to Istio Gateway (for mTLS, telemetry)
4. Routed to Nginx Service as lb
5. Routed to Nginx Pods
6. Forwarded to `photoapp-svc`  
7. Pod(s) in `photoapp` Deployment serve the content  

---

## Deployment Steps

1. Clone this repository  
   ```bash
   git clone https://github.com/Omariibrahem/Photo-Store-App--K8S-Deployment.git
   cd Photo-Store-App--K8S-Deployment
   ```  
2. (Option A) **Plain kubectl**  
   ```bash
   kubectl apply -f Nginx.yml
   kubectl apply -f istioGW.yml
   kubectl apply -f photoapp.yml
   ```  
3. (Option B) **Kustomize**  
   ```bash
   kubectl apply -k kustomize/overlays/dev
   ```  
4. (Option C) **Helm**  
   ```bash
   helm repo add photo-store ./helm-chart
   helm install photo-store photo-store --namespace photo --create-namespace
   ```  
