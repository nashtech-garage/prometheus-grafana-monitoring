# prometheus-grafana-monitoring
This repo contains the template to run Prometheus, Grafana and demo application to demonstrate the monitoring stack on EKS

## Prerequisites

- Kubernetes 1.16+
- Helm 3+

## Get Helm Repository Info

```console
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

_See [`helm repo`](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Pull the Helm chart to local PC and Install

```console
helm pull prometheus-community/kube-prometheus-stack --version 48.1.1
tar -xzf  kube-prometheus-stack-48.1.1.tgz

###Set following parameters in values.yaml based on the known issue: https://github.com/helm/charts/issues/11310
prometheus:
  prometheusSpec:
    podMonitorSelectorNilUsesHelmValues: false
    serviceMonitorSelectorNilUsesHelmValues: false
    ruleSelectorNilUsesHelmValues: false

###install prometheus-grfana
helm -n monitoring upgrade prometheus-grafana-stack --install  -f values.yaml kube-prometheus-stack
###update config if needed by following command:
helm -n monitoring upgrade prometheus-grafana-stack -f values.yaml kube-prometheus-stack
```

```console
helm repo add stable/prometheus-adapter
###update config in values.yaml and install the prometheus-adapter stack using following command:
helm upgrade --install -f values.yaml prometheus-adapter  prometheus-adapter -n monitoring
helm upgrade -f values.yaml prometheus-adapter  prometheus-community/prometheus-adapter -n monitoring
```

```console
###Deploy the apps using following commands:
kubectl -n nodejs apply -f nodejs-app/
kubectl -n nginx apply -f sample-app/
```

```console
###access prometheus, grafana, alertmanager UI by using:
kubectl -n monitoring port-forward svc/prometheus-grafana-stack-k-prometheus 9000:9090
kubectl -n monitoring port-forward svc/kube-stack-prometheus-grafana 8080:80
kubectl -n monitoring port-forward svc/prometheus-grafana-stack-k-alertmanager 9093
```