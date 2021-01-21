# Kubernetes - ReplicaSet
## Crear un objeto de tipo replicaSet a partir del [pod](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_1) con las siguientes especificaciones:

•  Debe tener 3 replicas

• ¿Cúal sería el comando que utilizarías para escalar el número de replicas a 10?

• Si necesito tener una replica en cada uno de los nodos de Kubernetes, ¿qué objeto se adaptaría mejor?

## Desarrollo

Configuramos nuestro archivo replicaset.yaml con las siguientes especificaciones:

**replicaset.yaml**
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    app: nginx-rs
    tier: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: backend
  template:
    metadata:
      labels:
        tier: backend
      name: nginx-v1
    spec:
      containers:
      - image: nginx:1.19.4
        name: nginx
        resources:
          requests:
            memory: "256Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "100m"
```
Ahora creamos el ReplicaSet a partir de nuestro archivo de configuración replicaset.yaml:

```sh

$ kubectl create -f replicaset.yaml

$ kubectl get all

```
Una vez creado con **kubectl get all** podemos ver las 3 nuevas replicas de nuestro pod nginx y el replicaset configurado a 3 replicas:

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/replica1.0.png  "ReplicaSet")

### ¿Cúal sería el comando que utilizarías para escalar el número de replicas a 10?

Ahora para escalar el numero de replicas a 10....

```sh

$ kubectl scale --replicas=10 replicaset nginx-rs

```
para visualizar el resultado:

```sh

$ kubectl get all

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/replica1.1.png  "ScaleReplicaSet")

### Si necesito tener una replica en cada uno de los nodos de Kubernetes, ¿qué objeto se adaptaría mejor? 

- **DaemonSet** : un DaemonSet garantiza que todos los nodos ejecuten una replica de un Pod, el emplazamiento es especifico


