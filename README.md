# Infrastructure as Code (IaC) handling monitoring related software and configurations


## Install the Operator (automated installer of all things from Elastic (the company))
kubectl create -f https://download.elastic.co/downloads/eck/2.2.0/crds.yaml
kubectl apply -f https://download.elastic.co/downloads/eck/2.2.0/operator.yaml

## Set a declaration that will be detected by the Operator that will install Elastic Search
```yaml
kubectl update ns elasticsearch
cat <<EOF | kubectl apply -f -
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: elasticsearch
  namespace: elasticsearch
spec:
  version: 8.2.0
  http:
    tls:
      selfSignedCertificate:
        disabled: true
  nodeSets:
  - name: default
    count: 1
    config:
      node.store.allow_mmap: false
EOF
```

## See installation progress of Elastic Search
`watch kubectl get all -n elasticsearch`

## Expose via Istio
`kubectl apply -f vs-elasticsearch.yaml`
## Retrieve the password for user 'elastic'
`PASSWORD=$(kubectl get secret elasticsearch-es-elastic-user -n elasticsearch -o go-template='{{.data.elastic | base64decode}}')`

## Execute
`curl -u "elastic:$PASSWORD" https://stage-api.verituityplatform.com/elasticsearch`

## References

* https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-quickstart.html

### Who do I talk to? ###

* See the commit log!