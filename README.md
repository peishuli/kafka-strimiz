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

# After the connector instance is registered, the cdc topics should be automatically created and can be displayed with the following command (view available topics)
kubectl exec my-cluster-kafka-0 -- bin/kafka-topics.sh --list --zookeeper localhost:2181

# monitory the cdc topics
kubectl exec -i my-cluster-kafka-0 -- bin/kafka-console-consumer.sh \
--bootstrap-server my-cluster-kafka-bootstrap:9092 \
--from-beginning \
--property print.key=true \
--topic server1.dbo.customers

# view connectors
kubectl exec -i my-cluster-kafka-0 -- curl -X GET http://my-connect-connect-api:8083/connectors
	
	
# run sql statements
kubectl exec -it sqlserver-69f78d47f-bfbq9 -- /bin/bash -c '/opt/mssql-tools/bin/sqlcmd -U sa -P Password!'

```