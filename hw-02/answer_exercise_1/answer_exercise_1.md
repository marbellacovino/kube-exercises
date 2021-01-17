# Kubernetes - Pods 
## Crea un pod de forma declarativa con las siguientes especificaciones:

• Imagen: nginx

• Version: 1.19.4

• Label: app: nginx-server

• Limits

CPU: 100 milicores

Memoria: 256Mi

• Requests

CPU: 100 milicores Memoria: 256Mi

### Realiza un despliegue en Kubernetes, y responde las siguientes preguntas:

• ¿Cómo puedo obtener las últimas 10 líneas de la salida estándar (logs generados por la aplicación)?

• ¿Cómo podría obtener la IP interna del pod? Aporta capturas para indicar el proceso que seguirías.

• ¿Qué comando utilizarías para entrar dentro del pod?

• Necesitas visualizar el contenido que expone NGINX, ¿qué acciones

debes llevar a cabo?

• Indica la calidad de servicio (QoS) establecida en el pod que acabas de

crear. ¿Qué lo has mirado?

## Desarrollo

  

Revisar las especificaciones de como construir un pod y sus resources:

```sh

$ kubectl explain pod.spec

$ kubectl explain pod.spec.containers.resources

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.0.png  "Pod Specs")

  

Construimos el archivo pod.yaml con las especificaciones indicadas:

  
**pod.yaml**

```yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    app: nginx-server
  name: nginx-server-v1
spec:
  containers:
  - image: nginx:1.19.4
    name: nginx-server
    resources:
      requests:
        memory: "256Mi"
        cpu: "100m"
      limits:
        memory: "256Mi"
        cpu: "100m"
  dnsPolicy: Default
  restartPolicy: Never

```

Ahora desplegamos el pod a partir del pod.yaml previamente configurado:

```sh

$ kubectl create -f answer_exercise_1/pod.yaml

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.1.png  "Created Pod")

  

Verificamos que nuestro pod se ha creado correctamente:

```sh

$ kubectl get pods

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.1.png  "Running Pod")

Una vez mi pod esta corriendo podemos responder las siguientes preguntas:

**• ¿Cómo puedo obtener las últimas 10 líneas de la salida estándar (logs generados por la aplicación)?**
```sh
$ kubectl logs --tail=10 nginx-server-v1
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.3.png  "Logs")

**• ¿Cómo podría obtener la IP interna del pod?**
```sh
$ kubectl get pods -o wide
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.4.png  "Pod Ip")

Podemos ver que la IP del pod es la 172.17.0.5
o tambien con el siguiente comando podemos ver todos los detalles del pod...
```sh
kubectl describe pod nginx-server-v1
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.5.png  "Pod Describe")

**• ¿Qué comando utilizarías para entrar dentro del pod?**
```sh
$ kubectl exec -it nginx-server-v1 -- bash
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.6.png  "Pod Bash")

**• Necesitas visualizar el contenido que expone NGINX, ¿qué acciones debes llevar a cabo?**

Podemos hacer port-forward del trafico del puerto 80 (Nginx expose port) al puerto 8080 (local port de mi machine) :
```sh
$ kubectl port-forward pod/nginx-server-v1 8080:80
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.8.png  "Port Forward")

Ahora desde otro terminal nos conectamos a la 127.0.0.1:8080 para visualizar el contenido NGINX

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.9.png  "Port Forward")

Otra manera es creando un servicio de tipo ClusterIP, que permite la comunicación interna en los nodos de mi cluter, para esto hacemos lo siguiente:

Indicamos como **--port** el puerto 80, el puerto en el que el contenedor de NGINX acepta tráfico 
```sh
$ kubectl expose pod/nginx-server-v1 --port 80 --name nginx-server-v1
$ kubectl get services
```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.12.png  "ClusterIP")

Con nuestro servicio listo, ya podemos visualizar el contenido de Nginx. Primero debemos entrar dentro del pod...
```sh
$ kubectl exec -it nginx-server-v1 -- bash
```
y desde el pod nos conectamos al puerto 80 para acceder al servicio que creamos
```sh
root@nginx-server-v1:/# curl localhost:80
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.13.png  "NGINX")

**• Indica la calidad de servicio (QoS) establecida en el pod que acabas de crear. ¿Qué lo has mirado?**

El siguiente comando nos permite ver todas las caracteristicas de mi pod:
```sh
$ kubectl describe pod nginx-server-v1
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.7.png  "Pod QoS")

Podemos ver que el QoS Class esta Guaranteed, esto ocurre por como hemos configurado el pod.yaml, en donde el pod debe tener un límite (limits) de memoria y una solicitud (requests) de memoria iguales y un límite (limits) de CPU y una solicitud (requests) de CPU iguales, es decir:

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube.1.11.png  "pod resources yaml")
