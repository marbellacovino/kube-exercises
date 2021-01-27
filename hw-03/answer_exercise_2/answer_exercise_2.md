# Kubernetes - [StatefulSet] 

## Crear un StatefulSet con 3 instancias de MongoDB
Se pide:

1. Habilitar el clúster de MongoDB

2. Realizar una operación en una de las instancias a nivel de configuración y
verificar que el cambio se ha aplicado al resto de instancias

3. Diferencias que existiría si el montaje se hubiera realizado con el objeto de
ReplicaSet

## Desarrollo parte 1: Habilitar el clúster de MongoDB

### Paso 1: 

Me conecto a mi cluster en la nube:

```sh

gcloud container clusters get-credentials cluster-2 --zone europe-west3-a --project kubernetes-hw02

```
Para configurar el conjunto de réplicas de MongoDB, se necesitan tres cosas: un **StorageClass**(ssd-storageclass.yaml), un **Headless Service**(mongo-svc.yaml) y un **StatefulSet**(mongo-statefulset.yaml):

```sh

kubectl create -f mongo-svc.yaml
kubectl create -f ssd-storageclass.yaml
kubectl create -f mongo-statefulset.yaml
kubectl get all

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.0.png  "stateful1.0")

### Paso 2: 

Configurar el ReplicaSet y el administrador de MongoDB...

Primero voy a ejecutar el siguiente comando:

```sh

kubectl get pods --watch

```

Desde otro terminal:

```kubectl

kubectl exec -it pod/mongo-0 -- bash
hostname -f

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.1.png  "stateful1.1")

Entro a MongoDB y reviso su estado:

```sh

mongo
rs.status()

```
Podemos ver que aun no se hay nada configurado:

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.2.png  "stateful1.2")

Ahora vamos a configurar el ReplicaSet de MongoDB como se muestra a continuación: 

```sh

rs.initiate({ _id: "rs0", version: 1, members: [ 
 { _id: 0, host: "mongo-0.mongo-svc.default.svc.cluster.local:27017" }, 
 { _id: 1, host: "mongo-1.mongo-svc.default.svc.cluster.local:27017" }, 
 { _id: 2, host: "mongo-2.mongo-svc.default.svc.cluster.local:27017" } 
]});

```

donde **mongo-0.mongo-svc.default.svc.cluster.local** es nuestro hostname

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.3.png  "stateful1.3")

Si ahora a reviso la configuración:

```sh

rs.config()

``` 

Vemos que el replicaset de MongoDB ha sido configurado...

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.4.png  "stateful1.4")

## Desarrollo parte 2: Realizar una operación en una de las instancias a nivel de configuración y verificar que el cambio se ha aplicado al resto de instancias

### Paso 1:

Modifico la instancia mongo-0 como se muestra a continuación:

```sh

kubectl exec -it pod/mongo-0 -- bash
mongo
show dbs

```
Creo una nueva db:
```sh

use students

```

Agrego datos a mi database con el comando db.<collection>.insert:

```sh

db.user.insert({name: "Marbella Covino", age: 28})

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.5.png  "stateful1.5")

### Paso 2:

Verifico los cambios realizados en la instancia mongo-1:

```sh

kubectl exec -it pod/mongo-1 -- bash
mongo
rs.secondaryOk()
show dbs
use students
show tables
db.user.find()

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.6.png  "stateful1.6")

## Desarrollo parte 3: Diferencias que existiría si el montaje se hubiera realizado con el objeto de ReplicaSet

StatefulSet proporciona garantía sobre el orden y la exclusividad de los pods. Cada replica tendrá su propio estado y cada uno de los pods creará su propio PVC. Entonces, un StatefulSet con 3 réplicas creará 3 pods, cada uno con su propio volumen, por lo que habra un total de 3 PVC. 

Los StatefulSets son valiosos para aquellas aplicaciones que necesitan uno o más de los siguientes:

• Identificadores de red estables, únicos.

• Almacenamiento estable, persistente.

• Despliegue y escalado ordenado, controlado.

• Actualizaciones en línea ordenadas, automatizadas.

El ReplicaSet se usa para aplicaciones sin estado, NO tendriamos las caracterícas que nos proporciona un StatefulSet. Por lo tanto, si una de mis replicas muere, el servicio levantara otra nueva con un identificador diferente por lo que perderiamos el estado de replica anterior. Sin embargo podemos guardar el estado si se adjunta un volumen persistente, pero todos los pods de esta implementación compartirán el mismo volumen y los datos en todos ellos serán los mismos. 

