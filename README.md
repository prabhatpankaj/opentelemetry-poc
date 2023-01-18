# setting up clickhouse cluster

```
kubectl create namespace clickhouse

curl -s https://raw.githubusercontent.com/Altinity/clickhouse-operator/master/deploy/operator-web-installer/clickhouse-operator-install.sh | OPERATOR_NAMESPACE=clickhouse bash

kubectl apply -f clickhouse/zookeeper-1-node.yaml -n clickhouse
kubectl apply -f clickhouse/cluster.yaml -n clickhouse

```

# setting up open-telemetry cluster
```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts

helm repo update

kubectl create namespace opentelemetry-system

helm show values open-telemetry/opentelemetry-operator > opentelemetry-operator/values.yaml

helm upgrade --install opentelemetry-operator open-telemetry/opentelemetry-operator -n opentelemetry-system -f opentelemetry-operator/values.yaml

helm show values open-telemetry/opentelemetry-collector > opentelemetry-collector/values.yaml

helm upgrade --install opentelemetry-collector open-telemetry/opentelemetry-collector -n opentelemetry-system -f opentelemetry-collector/values.yaml

```
