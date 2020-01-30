# Instructions 

If you wanted persistence, you will have to create a PersistentVolumeClaim (PVC) as indicated [here](https://strimzi.io/2019/02/28/resizing-persistent-volumes.html). For local (Docker Desktop) cluster, I created the pvc.yaml file which points to "hostpath" (the default) storage class.

Alternatively, set spec.kafka.storage.type to ephemeral

## To Set It Up

```
k craete ns kafka
k apply -f pvc
```

## Reference

- [Quick Starts on Minikube](https://strimzi.io/quickstarts/minikube/)
- [Setup Kafka](https://robertbrem.github.io/Microservices_with_Kubernetes/19_CQRS_with_Kafka/01_Setup_Kafka/)
