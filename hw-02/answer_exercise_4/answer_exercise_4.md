# Kubernetes - Deployment Strategies
## Crear un objeto de tipo deployment con las especificaciones del [ejercicio 1](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_1).

• Despliega una nueva versión de tu nuevo servicio mediante la técnica “recreate”

• Despliega una nueva versión haciendo “rollout deployment”

• Realiza un rollback a la versión generada previamente


#### NEW
From dockerHub directory ...

Crear una custom imagen de nginx:1.19.4 a partir de un Dockerfile y subirla al dockerHub como mi version 1.0 indicando en el tag v1

Primero creamos nuestro index.html personalizado que va a sustituir el index.html de la imagen base de nginx.

**index.html**

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Docker Nginx</title>
</head>
<body>
  <h2>Hello from Nginx nginx:1.19.4</h2>
</body>
</html>
```

Ahora creamos nuestro Dockerfile con las siguientes instrucciones:

**Dockerfile**

```
FROM nginx:1.19.4
# Copia nuestro custom index.html al directorio que contiene el html de la imagen nginx 
COPY index.html /usr/share/nginx/html/index.html
```

Finalmente podemos construir nuestra custom image y subirla al DockerHub con los siguientes comandos:

```
$ docker login

$ docker build -t marbellacovino/nginx:1.0 .   

$ docker push marbellacovino/nginx:1.0 
```

Ahora creo otra version de nginx a partir del mismo Dockerfile, para esto edito el Dockerfile con la imagen nginx:1.19.5 :

```
FROM nginx:1.19.5
# Copy index into the directory inside the container
COPY index.html /usr/share/nginx/html/index.html
```

Edito tambien el index.html...

**index.html**

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Docker Nginx</title>
</head>
<body>
  <h2>Hello from Nginx nginx:1.19.5</h2>
</body>
</html>
```

Y repito los pasos anteriores, indicando en el tag mi nueva version:

```
$ docker login

$ docker build -t marbellacovino/nginx:2.0 .   

$ docker push marbellacovino/nginx:2.0 
```

#### NEW

## Desarrollo

Creamos nuestro objeto de tipo deployment, para esto configuramos nuestro deployment.yaml como se muestra a continuación:

**deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx-server
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-server
  template:
    metadata:
      labels: 
        app: nginx-server
    spec:
      containers:
      - image: marbellacovino/nginx:1.0
        name: nginx
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

### Despliegue mediante recreación:

Todos los pods actuales se eliminan antes de que los nuevos se creen cuando .spec.strategy.type==Recreate.

Esto podemos hacerlo de dos maneras:

**1.** Editando el deployment.yaml directamente:

Para desplegar nuestro deployment con la estrategia _Recreate_, debemos editar nuestro deployment.yaml con la siguiente caracteristíca:

```yaml
spec:
  strategy:
    type: Recreate
```
Desde el directorio answer_exercise_4 creamos nuestro deployment con el siguiente comando:

```sh

$ kubectl create -f deployment.yaml --record

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/recreate1.0.png  "Recreate1.0")


Reviso la version de mi pod con el siguiente comando:

```sh

$ kubectl describe pod 

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/recreate1.2.png  "Recreate1.2")

```sh

Despliego una nueva versión de mi servicio...

$ kubectl set image deployment nginx-deployment nginx=marbellacovino/nginx:2.0 --record

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/recreate1.1.png  "Recreate1.1")

Si ahora reviso la versión de la imagen que mi pod utiliza, puedo ver que mis pods estan corriendo la nueva imagen de mi servicio...

```sh

$ kubectl describe pod 

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/recreate1.3.png  "Recreate1.3")







**2.**  Hacer un patch para actualizar el deployment.yaml usando la estrategia de retainKeys

```sh

$ kubectl patch deployment nginx-deployment -p '{"spec":{"strategy":{"$retainKeys":["type"] ,"type":"Recreate"}}}' --record

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/deploymentRecreate1.0.png  "DeploymentRecreatePatch")

Ahora para asegurarnos que el patch fue realizado podemos verificar el contenido del deployment.yaml con el siguiente comando:

```sh

$ kubectl describe deployment nginx-deployment 

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/deploymentRecreate1.1.png  "DeploymentRecreatePatch")

Observamos que el StrategyType ha sido modificado a Recreate


### Despliega una nueva versión haciendo “rollout deployment”

En un terminal...

```sh

$ kubectl set image deployment nginx-deployment nginx=nginx:1.19.5 --record

```
Y en otro ejecutar..

```sh

$ kubectl get pods --watch

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/rolloutDeployment1.0.png  "rolloutDeployment")

```sh

$  kubectl describe pod nginx-deployment-645d4c6787-pwdhd

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/rolloutDeployment1.1.png  "rolloutDeployment")




### Realiza un rollback a la versión generada previamente

Para hacer un rollback debemos saber a que versión queremos volver, para esto corremos el siguiente comando:

```sh

$  kubectl rollout history deployment nginx-deployment

```
En el history deployment podemos ver que la versión anterior es la 1 (REVISION=1)

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/rolloutDeployment1.3.png  "rolloutDeployment")

Primero ejecutamos el siguiente comando para visualizar los cambios que vamos a realizar...

```sh

$ kubectl get pods --watch

```
Ahora para volver a esa versión debemos correr el siguiente comando en un terminal diferente, indicando en **--to-revision** la versión anterior...

```sh

$  kubectl rollout undo deployment nginx-deployment --to-revision=1

```
Como podemos ver en la siguiente imagen, al realizar un rollback se elimina el pod viejo y se genera uno nuevo llamado _nginx-deployment-5cfbbf5d48-6cthw_

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/rollbackDeployment1.0.png  "rolloutDeployment")

Si ahora revisamos la version con la que este pod fue generado, vemos que el imagen utilizada es la versión anterior nginx:1.19.4

```sh

$  kubectl describe pod nginx-deployment-5cfbbf5d48-6cthw

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/rollbackDeployment1.2.png  "rolloutDeployment")

Podemos tambien revisar el rollout history para revisar la versión actual que esta utilizando el deployment


![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/rollbackDeployment1.3.png  "rolloutDeployment")
