# Deploying nginx ingress and setting up TLS in the Prod Monitoring cluster

## Install cert-manager in the Prod Monitoring k8s cluster
`kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.8.0/cert-manager.yaml`

## Create ClusterIssuer resource for Let’s encrypt certificates
`Kubectl apply –f clusterissuer.yaml`

## Deploy nginx ingress controller
`kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.0/deploy/static/provider/cloud/deploy.yaml`

## Create ingress resource to expose Kibana and Elasticsearch through nginx ingress
`kubectl apply -f kibana-ingress.yaml`
`kubectl apply -f elasticsearch-ingress.yaml`

## Patch nginx ingress controller to allow large log files to flow
`kubectl -n ingress-nginx patch configmap ingress-nginx-controller -p '{"data": {"proxy-body-size": "5tb"}}'`

## Get the external IP of the ingress controller
`kubectl get svc ingress-nginx-controller -n ingress-nginx`

## Create DNS A records for elasticsearch and kibana in Azure DNS zone (verituityplatform.com)

### For elasticsearch.verituityplatform.com
- Name: elasticsearch
- Type: A
- IP Address: <external ip of ingress controller>

### For kibana.verituityplatform.com
- Name: kibana
- Type: A
- IP Address: <external ip of ingress controller>
