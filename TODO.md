# TODO

Commands I run to make things work.

Run this, with some time in between:

```shell
kubectl -n invenio-dev create secret docker-registry regcred --docker-server=daffyregistry01.azurecr.io --docker-username="${REG_USERNAME}" --docker-password="${REG_PASSWORD:?}"
kubectl create secret generic nginx-snakeoil-tls-key --from-file=test.key=./test.key -n invenio-dev
kubectl create secret generic nginx-snakeoil-tls-crt --from-file=test.crt=./test.crt -n invenio-dev
helm install -n invenio-dev invenio ./charts/invenio/ -f ./kth-values.yaml
kubectl exec -it "$(kubectl get pods -l app=worker -o name)" -- /opt/invenio/src/wipe_recreate.sh
kubectl delete "$(kubectl get pods -l app=web -o name)"
kubectl exec -it "$(kubectl get pods -l app=worker -o name)" -- invenio alembic upgrade
kubectl exec -it "$(kubectl get pods -l app=worker -o name)" -- invenio queues declare
sudo kubectl port-forward svc/web 443:443
```
