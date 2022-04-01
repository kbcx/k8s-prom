# k8s-prom

only test on kubernetes v1.24.x

ref

- [prometheus](https://github.com/stefanprodan/k8s-prom-hpa/tree/master/prometheus) deploy Prometheus Server
- [prometheus-adapter](https://github.com/kubernetes-sigs/prometheus-adapter/tree/master/deploy/manifests) deploy custom-metrics-apiserver
- grafana
  - [grafana.yaml](https://grafana.com/docs/grafana/latest/installation/kubernetes/#create-grafana-kubernetes-manifest)

## step

```
# 1. create namespace
kubectl create namespace prom

# 2. use kubernetes ca sign serving key
openssl genrsa -out serving.key 2048
openssl req -new -key serving.key -out serving.csr -subj "/CN=serving"
openssl x509 -req -in serving.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out serving.crt -days 3650

# 3. create prometheus cm-adapter-serving-certs
kubectl create secret generic cm-adapter-serving-certs --from-file=./serving.key --from-file=./serving.crt -n prom

# 4. deploy prometheus server
kubectl apply -f ./prometheus

# 5. deploy prometheus-adapter
kubectl apply -f ./prometheus-adapter

# 6. deploy grafana, default account `admin/admin`
kubectl apply -f grafana/grafana.yaml
```
