# Kubernetes - ReplicaSet
## Crear un objeto de tipo replicaSet a partir del [pod](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_1) con las siguientes especificaciones:
• Debe tener 3 replicas
• ¿Cúal sería el comando que utilizarías para escalar el número de replicas a
10?
• Si necesito tener una replica en cada uno de los nodos de Kubernetes, ¿qué objeto se adaptaría mejor? (No es necesario adjuntar el objeto)

## Desarrollo

Configuramos nuestro archivo .yaml con las siguientes especificaciones:

**replicaset.yaml**
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-server
  labels:
    app: nginx-server
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
    spec:
      containers:
      - name: nginx-server
        image: nginx:1.19.4
```

```sh

$ kubectl create -f answer_exercise_2/replicaset.yaml

$ kubectl get all

```
Podemos ver que tenemos 3 nuevas replicas de nuestro pod nginx:

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/replica1.0.png  "ReplicaSet")

## ¿Cúal sería el comando que utilizarías para escalar el número de replicas a 10?

Ahora para escalar el numero de replicas a 10....

```sh

$ kubectl scale --replicas=10 replicaset nginx-server

```
para visualizar el resultado:

```sh

$ kubectl get all

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/replica1.1.png  "ScaleReplicaSet")

## Si necesito tener una replica en cada uno de los nodos de Kubernetes, ¿qué objeto se adaptaría mejor? 

- DaemonSet : un DaemonSet garantiza que todos los nodos ejecuten una replica de un Pod, el emplazamiento es especifico


