# Monitoring Stack - GitOPs Deployment

## Create a Namespace - Monitoring

`kubectl create ns monitoring`


## New Improvement on your app 

```
This project provisions a full monitoring stack on a Kubernetes cluster using GitOps with ArgoCD. It includes:

- ArgoCD → GitOps control plane
- Kube-Prometheus-Stack → Prometheus, Alertmanager, Grafana
- Loki → Logs aggregation with Promtail
- Tempo → Distributed tracing
- Student Progress App → Sample FastAPI app instrumented with health checks & metrics

## Project Structure  project Repo - https://github.com/ChisomJude/gitops-observability
```
gitops-observability/
└── argo/
    ├── root/
    │   └── app-of-apps.yaml        # Root Argo Application (App of Apps pattern)
    ├── monitoring/
    │   ├── kube-prometheus-stack.yaml # ArgoCD Application for kube-prometheus-stack
    │   ├── loki.yaml                  # ArgoCD Application for Loki
    │   ├── tempo.yaml                 # ArgoCD Application for Tempo
    │   └── grafana/
    │       └── datasources.yaml       # (Optional) Custom Grafana datasources (Loki, Tempo)
    └── optional/
        └── argocd-self-manage.yaml    # (Optional) Manage ArgoCD itself with Argo

```


We will use the App repo for Week 7, but slightly modified  on a new branch `monitoring ` - [SEE REPO](https://github.com/ChisomJude/complete-app-deployment-with-gitops/tree/monitoring) so app can accomodate monitoring

 The following files are the New Changes to your to your week 7 App repo, 
```
requirement.txt
dockerfile
src/app/main.py
helm/student-progress/templates/deployment.yaml
helm/student-progress/values.yaml
helm/student-progress/templates/servicemonitor.yml
```



## Apply in the cluster 
```
# Create project
kubectl create ns monitoring
kubectl apply -f argo/root/app-of-apps.yml
```

## Fort forward  and view your Deployment on the UI
```
kubectl -n argocd port-forward svc/argo-cd-argocd-server 8080:80 --address 0.0.0.0  #argo
kubectl -n monitoring port-forward svc/kps-grafana 3000:80 --address 0.0.0.0 #grafana

```