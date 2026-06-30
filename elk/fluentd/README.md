# Fluentd -> ELK VM setup (MERN only)

## 1) Sua cac gia tri bat buoc

1. Sua IP VM Elasticsearch tai file 03-daemonset.yaml
- FLUENT_ELASTICSEARCH_HOST: doi thanh IP hoac DNS cua VM ELK
- FLUENT_ELASTICSEARCH_PORT: mac dinh 9200

2. Sua namespace MERN tai file 02-configmap.yaml
- pattern namespace hien tai: ^(mern|mern-prod|mern-staging)$
- doi thanh namespace thuc te cua ban

3. Neu Elasticsearch co bat auth
- Tao secret tu mau 04-secret-example.yaml (doi username/password)
- Mo cac dong env secretKeyRef trong 03-daemonset.yaml
- Mo cac dong user/password trong 02-configmap.yaml


## 2) Apply theo thu tu

kubectl apply -f serviceaccount-rbac.yaml
kubectl apply -f configmap.yaml
kubectl apply -f daemonset.yaml

Neu co auth:
kubectl apply -f elk/fluentd/04-secret-example.yaml

Sau khi sua configmap, restart DaemonSet de nap cau hinh moi:
kubectl -n kube-system rollout restart daemonset/fluentd

## 3) Kiem tra Fluentd

kubectl -n kube-system get pods -l name=fluentd
kubectl -n kube-system logs -l name=fluentd --tail=200

Ky vong thay ket noi den Elasticsearch thanh cong va khong co loi retry lien tuc.

## 4) Kiem tra Elasticsearch/Kibana

- Index prefix: k8s-logs-mern
- Tao index pattern trong Kibana: k8s-logs-mern-*
- Kiem tra field kubernetes.namespace_name de chac chan chi nhan log namespace MERN
