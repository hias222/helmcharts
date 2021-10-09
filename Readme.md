# install

## create chart

cd swimcloud/charts
helm create datahub


## namespace

kubectl create -f admin/namespace-local.json

## ingress

minikube addons enable ingress

## deploy chart

helm upgrade --install swimcloud . \
  --namespace=local \
  --set displaysocket.image.tag="0.1.0" \
  --set frontend.image.tag="0.1.0" \
  --debug \
  --dry-run

helm upgrade --install swimcloud . --namespace=local --set displaysocket.image.tag="0.1.1" --set frontend.image.tag="0.1.0" --debug 

helm upgrade --install swimcloud . --namespace=local --set global.DEST_MQTT_MODE="MQTT" --debug --dry-run
helm upgrade --install swimcloud . --namespace=local --debug --dry-run

## check

helm list -A
helm uninstall swimcloud -n local

### get pods

kubectl get pods -n=local

### get logs

kubectl logs swimcloud-displaysocket-c77ff9cd8-f8lbh -n=local

### get web

kubectl get ingress -n=local
minikube ip
--> add to hots