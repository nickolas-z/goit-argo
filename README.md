# goit-argo

GitOps-репозиторій для ArgoCD Application, який розгортає MLflow та Nginx у namespace `application`.

## Структура

```text
.
├── application.yaml
├── namespaces/
│   ├── application/
│   │   ├── nginx.yaml   # Nginx Deployment + Service (smoke-test, синхронізується ArgoCD)
│   │   └── ns.yaml      # Namespace application (синхронізується ArgoCD)
│   └── infra-tools/
│       └── ns.yaml      # Namespace infra-tools (керується Terraform, не ArgoCD)
└── values/
    └── mlflow-values.yaml
```

## ArgoCD Application

`application.yaml` використовує ArgoCD multiple sources (три sources):

- **Source 1 — Helm chart:** `mlflow` з Helm repo `https://community-charts.github.io/helm-charts`
- **Source 2 — Values ref:** `values/mlflow-values.yaml` з цього репозиторію через `$values`
- **Source 3 — Manifests:** `namespaces/application/` з цього репозиторію (Namespace `application` + Nginx Deployment і Service)

> `namespaces/infra-tools/ns.yaml` у цьому репо — документаційний файл. Namespace `infra-tools` керується Terraform, ArgoCD його не синхронізує.

## Що деплоїться

| Ресурс | Namespace | Опис |
|---|---|---|
| MLflow | `application` | Helm chart `community-charts/mlflow` з `values/mlflow-values.yaml` |
| Nginx | `application` | Deployment + ClusterIP Service, smoke-test для перевірки ArgoCD |
| Namespace `application` | — | Створюється через `ns.yaml` (+ `CreateNamespace=true`) |

## Застосувати Application

```bash
kubectl apply -f application.yaml
kubectl get applications -n infra-tools
```

## Перевірити синхронізацію та поди

```bash
kubectl get pods -n application
kubectl get svc -n application
```

## Відкрити MLflow локально

```bash
kubectl -n application port-forward svc/mlflow 5000:5000
```

Після цього MLflow буде доступний на `http://localhost:5000`.

## Відкрити Nginx локально

```bash
kubectl -n application port-forward svc/nginx 8080:80
```

Після цього Nginx буде доступний на `http://localhost:8080`.
