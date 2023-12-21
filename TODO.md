# TODO

Commands I run to make things work.

Create this `ignore.kth-values.yaml`:

```yaml
haproxy:
  enabled: false

host: localhost

postgresql:
  enabled: true

search:
  enabled: true

web:
  image: ghcr.io/inveniosoftware/demo-inveniordm/demo-inveniordm@sha256:2193abc2caec9bc599061d6a5874fd2d7d201f55d1673a545af0a0406690e8a4
  replicas: 1

worker:
  image: ghcr.io/inveniosoftware/demo-inveniordm/demo-inveniordm@sha256:2193abc2caec9bc599061d6a5874fd2d7d201f55d1673a545af0a0406690e8a4
  replicas: 1
  resources:
    requests:
      cpu: "2"
      memory: 1Gi
    limits:
      cpu: "2"
      memory: 1Gi

workerBeat:
  resources:
    requests:
      cpu: "2"
      memory: 1Gi
    limits:
      cpu: "2"
      memory: 1Gi
```

Then run this, with some time in between:

```shell
kubectl create secret generic nginx-snakeoil-tls-key --from-file=test.key=./test.key -n invenio-dev
kubectl create secret generic nginx-snakeoil-tls-crt --from-file=test.crt=./test.crt -n invenio-dev
helm install -n invenio-dev invenio ./charts/invenio/ -f ./ignore.kth-values.yaml
kubectl exec -it "$(kubectl get pods -l app=worker -o name)" -- /opt/invenio/src/wipe_recreate.sh
kubectl delete "$(kubectl get pods -l app=web -o name)"
kubectl exec -it "$(kubectl get pods -l app=worker -o name)" -- invenio alembic upgrade
kubectl exec -it "$(kubectl get pods -l app=worker -o name)" -- invenio queues declare
kubectl port-forward svc/web 8080:80
```
