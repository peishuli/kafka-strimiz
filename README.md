# Instructions 

If you wanted persistence, you will have to create a PersistentVolumeClaim (PVC) as indicated [here](https://strimzi.io/2019/02/28/resizing-persistent-volumes.html). For local (Docker Desktop) cluster, I created the pvc.yaml file which points to "hostpath" (the default) storage class.

Alternatively, set spec.kafka.storage.type to ephemeral

## Install Strimzi k8s operator and depoy the sample kafka cluster (CR)

```
helm install strimzi/strimzi-kafka-operator
kubectl apply -f ./examples/kafka-ephemeral.yaml
kubectl apply -f ./examples/source-connector.yaml
kubectl apply -f ./examples/kafka-connect.yaml
kubectl apply -f debezium-kafka-connect.yaml

watch -n 1 kubectl get po -n kafka
```
