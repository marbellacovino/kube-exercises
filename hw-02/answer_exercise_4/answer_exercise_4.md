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

Para desplegar una versión de nuestro servicio con la estrategia _Recreate_, debemos editar nuestro .yaml con la siguiente caracteristíca:

```yaml
spec:
  replicas: 3
  strategy:
    type: Recreate
```
Nuestro archivo .yaml se vera de la siguiente manera:

**app-recreate.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ramped
  labels:
    app: nginx-server
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2  
      maxUnavailable: 0
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
Desde el directorio answer_exercise_4 creamos nuestro deployment con el siguiente comando:

```sh

$ kubectl create -f app-recreate.yaml
$ kubectl get pods

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/recreate1.0.png  "Recreate1.0")

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/recreate1.0.0.png  "Recreate1.0.0")

Revisamos la version actual de nuestro servicio con el siguiente comando:

```sh

$  kubectl port-forward pod/nginx-recreate-86779df7c7-7jcfs 8081:80

```
Abrimos nuestro navegador en el puerto 8081 para visualizar el contenido de pod. Podemos ver que la version actual es la nginx:1.19.4 (nuestra versión 1.0 en el DockerHub)

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/recreate1.2.png  "Recreate1.2")

Chequeo la versión de la imagen que mi pod utiliza con el siguiente comando:

```sh

$ kubectl describe pod 

```
En la seccion de eventos vemos que utiliza la nginx:1.0 

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/recreate1.3.png  "Recreate1.3")

Ahora despliego la versión 2.0 de mi servicio con el siguiente comando:

```sh

$ kubectl set image deployment nginx-recreate nginx=marbellacovino/nginx:2.0 --record

```

Si ahora reviso la versión de la imagen que mi pod utiliza con un _describe pod_, vemos ahora los pods utilizan la nginx:2.0

```sh

$ kubectl describe pod 

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/recreate1.4.png  "Recreate1.4")






### Despliega una nueva versión haciendo “rollout deployment”

Editamos nuestro .yaml con la siguiente caracteristíca:

```yaml
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2  
      maxUnavailable: 0
```

Desde el directorio answer_exercise_4 creamos nuestro deployment con el siguiente comando:

```sh

$ kubectl create -f app-ramped.yaml
$ kubectl get pods

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/ramped11.0.png  "Ramped1.0")

Revisamos la version actual de nuestro servicio con el siguiente comando:

 kubectl port-forward pod/nginx-ramped-86779df7c7-cmwxb 8080:80

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/ramped11.1.png  "Ramped1.1")

Podemos ver que la version actual es la nginx:1.19.4 (nuestra versión 1.0 en el DockerHub)

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/ramped1.2.png  "Ramped1.2")

Ahora Despliego una nueva versión de mi servicio...
```sh

$ kubectl set image deployment nginx-ramped nginx=marbellacovino/nginx:2.0 --record

```