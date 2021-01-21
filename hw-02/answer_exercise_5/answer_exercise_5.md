# Kubernetes - Blue Green Deployment
## Diseña una estrategia de despliegue que se base en ”Blue Green” con las especificaciones del [ejercicio 1](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_1).

## Desarrollo

Configuramos nuestro Blue Deployment deployment-v1.yaml como se muestra a continuación:

**deployment-v1.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-blue
  labels:
    app: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-blue
      version: blue
  template:
    metadata:
      labels: 
        app: nginx-blue
        version: blue
    spec:
      containers:
      - image: marbellacovino/nginx:1.0
        name: nginx-blue
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
Y ahora configuro el Green Deployment deployment-v2.yaml:

**deployment-v2.yaml**

```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-green
  labels:
    app: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      version: v2.0.0
  template:
    metadata:
      labels: 
        version: v2.0.0
    spec:
      containers:
      - image: marbellacovino/nginx:2.0
        name: nginx-blue
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

Desde el directorio answer_exercise_5 creamos nuestro blue deployment y servicio con los siguientes comandos:

```sh

$ kubectl apply -f deployment-v1.yaml

$ kubectl apply -f service.yaml

$ kubectl get services

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/bluegreen1.1.png  "Deployment")

Una vez tengo mi blue deployment corriendo y mi servicio configurado a este, creo mi green deployment con el siguiente comando:

```sh

$ kubectl apply -f deployment-v2.yaml

```

Ahora en un terminal hacemos un get de nuestros deployment y en otro un pod watch con los siguientes comandos:

```sh

$ kubectl get deployments
$ kubectl get pods --watch

```
Podemos ver que ya tenemos ambos deployments corriendo

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/bluegreen1.0.png  "Deployment")

Vamos a visualizar la version a la que mi servicio se encuentra attached ejecutando el siguiente comando:

```sh

$ kubectl describe service nginx-bluegreensvc

```
Observamos que mi servicio esta attached a la v1.0.0 (blue deployment)...

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/bluegreen1.2.png  "Deployment")

Nos conectamos al servicio a traves del puerto 30934...

```sh

$ minikube ip

$ curl 192.168.64.2:30934

```
El contenido de Nginx indica que estamos corriendo la version nginx:1.19.4, esta es la version que los pods de mi blue deployment estan corriendo.

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/bluegreen1.4.png  "Deployment")

Una vez mi green deployment esta "OK" ready to go... voy a modificar mi servicio para que reciba el trafico de mi green deployment con el siguiente comando:

```sh

$ kubectl patch service nginx-bluegreensvc -p '{"spec":{"selector":{"version":"v2.0.0"}}}'

$ kubectl describe service nginx-bluegreensvc

```
Al revisar la nueva configuración de mi servicio observamos que la versión es la v2.0.0

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/bluegreen1.5.png  "Deployment")

Me conecto de nuevo al puerto de mi servicio, para ver el contenido:

```sh

$ minikube ip

$ curl 192.168.64.2:30934

```

Ahora el contenido de Nginx indica que estamos corriendo la version nginx:1.19.5, esta es la version que los pods de mi green deployment estan corriendo.

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/bluegreen1.6.png  "Deployment")

Finalmente con el servicio corriendo la nueva versión de mi deployment (green deployment) puedo borrar mi blue deployment para no gastar recursos.

```sh

$ kubectl delete -f deployment-v1.yaml

```

 ![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/bluegreen1.7.png  "Deployment")