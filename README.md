##  Installation EFK on EKS

**create storageclass**
```
kubectl apply -f gp2-storage-class.yaml --namespace=kube-logging

kubectl patch storageclass gp2 -p '{"metadata":{"annotations:{"storageclass.kubernetes.io/is-default-class":"true"}}}' --namespace=kube-logging
```

**create namespace**
```
kubectl get namespaces
kubectl apply -f kube-logging.yaml
```

**create ElasticSearch Statefulset**
```
nano elasticsearch_svc.yaml
kubectl apply -f elasticsearch_svc.yaml
kubectl get services --namespace=kube-logging

kubectl apply -f elasticsearch_statefulset.yaml
kubectl rollout status sts/elasticsearch --namespace=kube-logging
kubectl port-forward elasticsearch-0 9200:9200 --namespace=kube-logging

#check es
curl http://localhost:9200/_cluster/state?pretty
```

**Creating the Kibana Deployment and Service**
```
kubectl apply -f kibana.yaml
kubectl rollout status deployment/kibana --namespace=kube-logging
kubectl get svc --namespace=kube-logging
kubectl get pods --namespace=kube-logging
```

**Creating the Fluentd DaemonSet**
```
kubectl apply -f fluentd.yaml

kubectl get ds --namespace=kube-logging
```

**deleting namespaces**
```
 kubectl get namespaces
 kubectl get namespace kube-logging -o yaml
 
 kubectl get namespace kube-logging -o json >tmp.json
 
 kubectl proxy
 
curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8001/api/v1/namespaces/kube-logging/finalize
```


