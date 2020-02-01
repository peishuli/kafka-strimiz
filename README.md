# Instructions 

If you wanted persistence, you will have to create a PersistentVolumeClaim (PVC) as indicated [here](https://strimzi.io/2019/02/28/resizing-persistent-volumes.html). For local (Docker Desktop) cluster, I created the pvc.yaml file which points to "hostpath" (the default) storage class.

Alternatively, set spec.kafka.storage.type to ephemeral

## Install Strimzi k8s operator and depoy the sample kafka cluster (CR) & install the connector plugin
```
helm repo add strimzi https://strimzi.io/charts/
helm init

helm install strimzi/strimzi-kafka-operator
kubectl apply -f ./examples/kafka-ephemeral.yaml
kubectl apply -f debezium-kafka-connect.yaml

watch -n 1 kubectl get po -n kafka
```

## Install SQL Server
**Note:** The pod will be different from the one shown below
```
kubectl apply -f mssql-server.yaml
cat db-scripts.sql | kubectl exec -it sqlserver-69f78d47f-6zxbs -- /bin/bash -c '/opt/mssql-tools/bin/sqlcmd -U sa -P Password!'
```

## Registry SQL Server connector instance & view/verify installed connectors
```
cat register-sqlserver.json | kubectl exec -i my-cluster-kafka-0 -- curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" http://my-connect-connect-api:8083/connectors -d @-

kubectl exec -i my-cluster-kafka-0 -- curl -X GET http://my-connect-connect-api:8083/connectors
```