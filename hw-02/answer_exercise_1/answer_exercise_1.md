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

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/master/hw-02/images/kube1.0.png  "Pod Specs")

  

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

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.10.png  "pod.yaml")



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

• ¿Cómo puedo obtener las últimas 10 líneas de la salida estándar (logs generados por la aplicación)?
```sh
$ kubectl logs --tail=10 nginx-server-v1
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.3.png  "Logs")

• ¿Cómo podría obtener la IP interna del pod? Aporta capturas para indicar el proceso que seguirías.
```sh
$ kubectl get pods -o wide
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.4.png  "Pod Ip")

o tambien...
```sh
kubectl describe pod nginx-server-v1
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.4.png  "Pod Describe")

• ¿Qué comando utilizarías para entrar dentro del pod?
```sh
$ kubectl exec -it nginx-server-v1 -- bash
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.6.png  "Pod Bash")

• Necesitas visualizar el contenido que expone NGINX, ¿qué acciones debes llevar a cabo?
Podemos hacer port-forward del trafico del puerto 80 (Nginx port) al puerto 8080 (local port de mi machine) :
```sh
$ kubectl port-forward pod/nginx-server-v1 8080:80
```
Otra manera es creando un servicio de tipo NodePort, que permite abrir un puerto para comunicarme con mi nodo desde el exterior, para esto hacemos lo siguiente:
```sh
$ kubectl expose node/nginx-server-v1 --port 80 --name nginx-server-v1 --type NodePort
```

• Indica la calidad de servicio (QoS) establecida en el pod que acabas de crear. ¿Qué lo has mirado?
El siguiente comando nos permite ver todas las caracteristicas de mi pod:
```sh
$ kubectl describe pod nginx-server-v1
```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.6.png  "Pod QoS")

Podemos ver que el QoS Class esta Guaranteed, esto ocurre por como hemos configurado el pod.yaml, en donde el pod debe tener un límite (limits) de memoria y una solicitud (requests) de memoria iguales y un límite (limits) de CPU y una solicitud (requests) de CPU iguales, es decir:

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/kube1.11.png  "pod resources yaml")
