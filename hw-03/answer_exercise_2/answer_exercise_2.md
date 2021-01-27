gcloud container clusters get-credentials cluster-2 --zone europe-west3-a --project kubernetes-hw02

```sh

kubectl create -f mongo-svc.yaml
kubectl create -f ssd-storageclass.yaml
kubectl create -f mongo-statefulset.yaml
kubectl get all

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.0.png  "stateful1.0")

• Habilitar el clúster de MongoDB

En un terminal :

```sh

kubectl get pods --watch

```

En otro terminal:

```kubectl

kubectl exec -it pod/mongo-0 -- bash
hostname -f

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.1.png  "stateful1.1")

```mongo

mongo
rs.status()

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.2.png  "stateful1.2")


Ahora habilitamos el cluster de MongoDB como se muestra a continuación: 

```sh

rs.initiate({ _id: "rs0", version: 1, members: [ 
 { _id: 0, host: "mongo-0.mongo-svc.default.svc.cluster.local:27017" }, 
 { _id: 1, host: "mongo-1.mongo-svc.default.svc.cluster.local:27017" }, 
 { _id: 2, host: "mongo-2.mongo-svc.default.svc.cluster.local:27017" } 
]});

```

donde **mongo-0.mongo-svc.default.svc.cluster.local** es nuestro hostname

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.3.png  "stateful1.3")

Si volvemos a revisar el status:

```sh

rs.status()

``` 

Vemos que el replicaset de MongoDB ha sido configurado...

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.4.png  "stateful1.4")

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer2/stateful1.5.png  "stateful1.5")

• Realizar una operación en una de las instancias a nivel de configuración y
verificar que el cambio se ha aplicado al resto de instancias


• Diferencias que existiría si el montaje se hubiera realizado con el objeto de
ReplicaSet