# setting up clickhouse cluster

* create namespace
```
kubectl create namespace clickhouse

```
* create clickhouse operator

```
curl -s https://raw.githubusercontent.com/Altinity/clickhouse-operator/master/deploy/operator-web-installer/clickhouse-operator-install.sh | OPERATOR_NAMESPACE=clickhouse bash
```

* download zookeeper one node

```
wget -O k8s/clickhouse/zookeeper-1-node.yaml https://raw.githubusercontent.com/Altinity/clickhouse-operator/master/deploy/zookeeper/quick-start-persistent-volume/zookeeper-1-node.yaml

```
* update namespace or volume size and install zookeeper one node

```
kubectl apply -f k8s/clickhouse/zookeeper-1-node.yaml -n clickhouse
```

for more type you can visit https://github.com/Altinity/clickhouse-operator/tree/master/deploy/zookeeper

* create ClickHouseInstallation

generate password_sha256_hex using https://workat.tech/developer-tools/sha256-hash-generator
* k8s/clickhouse/cluster.yaml
```
apiVersion: "clickhouse.altinity.com/v1"
kind: "ClickHouseInstallation"
metadata:
  name: "clickhousecl"
  namespace: clickhouse
spec:
  configuration:
    users:
      prabhat/networks/ip: "::/0"
      prabhat/password_sha256_hex: e8e3da929326b8635ab4051842e30327be566e4f2e4b14b0a076e2805e2416a1 
      prabhat/profile: default
    zookeeper:
        nodes:
        - host: zookeeper
          port: 2181
    clusters:
      - name: "clickhousecl"
        layout:
          shardsCount: 1
          replicasCount: 1
        templates:
          podTemplate: clickhouse-stable
          volumeClaimTemplate: storage-vc-template
  templates:
    podTemplates:
      - name: clickhouse-stable
        spec:
          containers:
          - name: clickhouse
            image: altinity/clickhouse-server:21.8.10.1.altinitystable
    volumeClaimTemplates:
      - name: storage-vc-template
        spec:
          accessModes:
            - ReadWriteOnce
          resources:
            requests:
              storage: 30Gi



kubectl apply -f k8s/clickhouse/cluster.yaml -n clickhouse

```
for more examples you can visit 
https://github.com/Altinity/clickhouse-operator/tree/master/docs/chi-examples

# setting up open-telemetry cluster

* create opentelemetry-operator

```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts

helm repo update

```
* review open-telemetry operator value

```
helm show values open-telemetry/opentelemetry-operator

```
* if you want to create file and then update the required change

```
helm show values open-telemetry/opentelemetry-operator > k8s/opentelemetry-operator/values.yaml

```
* install opentelemetry-operator
```
kubectl create namespace opentelemetry-system

helm upgrade --install opentelemetry-operator open-telemetry/opentelemetry-operator -n opentelemetry-system -f k8s/opentelemetry-operator/values.yaml

```
* review open-telemetry collector value

```
helm show values open-telemetry/opentelemetry-collector > k8s/opentelemetry-collector/values.yaml

```
* if you want to create file and then update the required change

```
helm show values open-telemetry/opentelemetry-collector > k8s/opentelemetry-collector/values.yaml

```
* install opentelemetry-collector-deployment

```
helm upgrade --install opentelemetry-collector-deployment open-telemetry/opentelemetry-collector -n opentelemetry-system -f k8s/opentelemetry-collector/deployment-values.yaml

```

* install opentelemetry-collector-daemonset

```
helm upgrade --install opentelemetry-collector-daemonset open-telemetry/opentelemetry-collector -n opentelemetry-system -f k8s/opentelemetry-collector/daemonset-values.yaml

```
