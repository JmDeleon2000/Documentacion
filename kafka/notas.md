# Instalar kafka usando Strimzi
https://strimzi.io/quickstarts/


# Configurar Kafka con Knative-eventing
## Instalar controlador de Kafka del lado de Knative
Los siguientes comandos instalan la capacidad de Knative para manejar a Kafka como Message Broker
```{bash}
kubectl apply --filename https://github.com/knative-sandbox/eventing-kafka-broker/releases/download/knative-v1.11.4/eventing-kafka-controller.yaml

kubectl apply --filename https://github.com/knative-sandbox/eventing-kafka-broker/releases/download/knative-v1.11.4/eventing-kafka-broker.yaml
```
## Verificar que se hayan creado los objetos
```{bash}
kubectl get deployments.apps -n knative-eventing
```
Se espera que estén creados:
```{bash}
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
eventing-controller            1/1     1            1           10s
eventing-webhook               1/1     1            1           9s
kafka-controller               1/1     1            1           3s
kafka-broker-dispatcher        1/1     1            1           4s
kafka-broker-receiver          1/1     1            1           5s
```
## Crear broker de Kafka en Knative
```{YAML}
apiVersion: eventing.knative.dev/v1
kind: Broker
metadata:
  annotations:
    # case-sensitive
    eventing.knative.dev/broker.class: Kafka
    # Optional annotation to point to an externally managed kafka topic:
    # kafka.eventing.knative.dev/external.topic: <topic-name>
  name: default
  namespace: default
spec:
  # Configuration specific to this broker.
  config:
    apiVersion: v1
    kind: ConfigMap
    name: kafka-broker-config
    namespace: knative-eventing
  # Optional dead letter sink, you can specify either:
  #  - deadLetterSink.ref, which is a reference to a Callable
  #  - deadLetterSink.uri, which is an absolute URI to a Callable (It can potentially be out of the Kubernetes cluster)
  delivery:
    deadLetterSink:
      ref:
        apiVersion: serving.knative.dev/v1
        kind: Service
        name: dlq-service
```

## Configurar broker de Kafka
**Importante:** Llenar **bootstrap.servers:** con lo que es. Lo demás se puede quedar como está.

```{YAML}
apiVersion: v1
kind: ConfigMap
metadata:
  name: kafka-broker-config
  namespace: knative-eventing
data:
  # Number of topic partitions
  default.topic.partitions: "10"
  # Replication factor of topic messages.
  default.topic.replication.factor: "3"
  # A comma separated list of bootstrap servers. (It can be in or out the k8s cluster)
  bootstrap.servers: "my-cluster-kafka-bootstrap.kafka:9092"
```