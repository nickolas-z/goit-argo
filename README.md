# goit-argo

GitOps-репозиторій для ArgoCD Application, який розгортає MLflow у namespace `application`.

## Структура

```text
.
├── application.yaml
├── namespaces/
│   ├── application/
│   │   ├── nginx.yaml
│   │   └── ns.yaml
│   └── infra-tools/
│       └── ns.yaml
└── values/
    └── mlflow-values.yaml
```

## ArgoCD Application

`application.yaml` використовує ArgoCD multiple sources:

- Helm chart: `mlflow` з Helm repo `https://community-charts.github.io/helm-charts`
- Values: `values/mlflow-values.yaml` з цього репозиторію через `$values`

Застосувати Application після встановлення ArgoCD:

```bash
kubectl apply -f application.yaml
kubectl get applications -n infra-tools
```

Перевірити синхронізацію та поди:

```bash
kubectl get pods -n application
kubectl get svc -n application
```

Відкрити MLflow локально:

```bash
kubectl -n application port-forward svc/mlflow 5000:5000
```
