# Kubernetes - Rollout & Rollback
## Crear un objeto de tipo deployment con las especificaciones del [ejercicio 1] (https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_1).

1. Despliega una nueva versión de tu nuevo servicio mediante la técnica “recreate”

2. Despliega una nueva versión haciendo “rollout deployment”

3. Realiza un rollback a la versión generada previamente

## Desarrollo

Despliegue mediante recreación
Todos los Pods actuales se eliminan antes de que los nuevos se creen cuando .spec.strategy.type==Recreate.

Configuramos nuestro yaml como se muestra a continuación:

**deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx-server
spec:
  selector:
    matchLabels:
      app: nginx-server 
  template:
    metadata:
      labels: 
        app: nginx-server
    spec:
      containers:
      - image: nginx:1.19.4
        name: nginx-v1
        resources:
          requests:
            memory: "256Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "100m"
        ports:
          - containerPort: 80
```

Y creamos nuestro deployment con el siguiente comando:

```sh

$ kubectl create -f answer_exercise_4/deployment.yaml --record

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/deployment.png  "Deployment")

**1. Despliega una nueva versión de tu nuevo servicio mediante la técnica “recreate”**

Para desplegar nuestro deployment con la estrategia _Recreate_, debemos editar nuestro deployment.yaml con la siguiente caracteristíca:

```yaml
spec:
  strategy:
    type: Recreate
```

Esto podemos hacerlo de dos maneras:

• Hacer un patch para actualizar el deployment.yaml usando la estrategia de retainKeys

```sh

$ kubectl patch deployment nginx-deployment -p '{"spec":{"strategy":{"$retainKeys":["type"] ,"type":"Recreate"}}}'

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/deployment1.0.png  "DeploymentRecreatePatch")

Ahora para asegurarnos que el patch fue realizado podemos verificar el contenido del deployment.yaml con el siguiente comando:

```sh

$ kubectl describe deployment nginx-deployment 

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/deployment1.1.png  "DeploymentRecreatePatch")

Observamos que el StrategyType ha sido modificado a Recreate

• Editando el deployment.yaml directamente:

```sh

$ kubectl edit deployment nginx-deployment

```

**2. Despliega una nueva versión haciendo “rollout deployment”**



