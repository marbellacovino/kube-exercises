# Kubernetes - Services
## Crea un objeto de tipo service para exponer la [aplicación](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_2) de las siguientes formas:

1. Exponiendo el servicio hacia el exterior (crea service1.yaml)

2. De forma interna, sin acceso desde el exterior (crea service2.yaml)

3. Abriendo un puerto especifico de la VM (crea service3.yaml)

## Desarrollo

**1. Exponiendo el servicio hacia el exterior (crea service1.yaml)**

Para esto debemos crear un servicio de tipo LoadBalancer

Configuramos nuestro yaml como se muestra a continuación:

**service1.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-lb
spec:
  type: LoadBalancer
  selector:
    app: nginx-server
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

Primero debemos iniciar googleCloud y conectarnos a nuestro cluster, esto lo hacemos desde la línea de comandos de kubectl ejecutando:

```sh
$ gcloud init

$ gcloud container clusters get-credentials cluster-1 --zone europe-west3-b --project kubernetes-hw02

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/gcloud1.0.png  "Load Balancer Service")

Ahora vamos a listar todos los componentes de mi  gcloud cluster con el siguiente comando:

```sh

$ kubectl get all 

```
Como podemos ver el cluster todavia no tiene pods, servicios o deployments...

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/service1.0.png  "Load Balancer Service")

Ahora que estamos en nuestro cluster en la nube vamos a crear el replicaSet a partir del replicaset.yaml del apartado anterior y el servicio load balancer (service1.yaml) previamente configurado.

Desde la línea de comandos de kubectl ejecutamos:

```sh

$ kubectl create -f answer_exercise_2/replicaset.yaml

$ kubectl create -f answer_exercise_3/service1.yaml  

$ kubectl get services

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/service1.1.png  "Load Balancer Service")

Una vez asignada la <external-ip>  a mi servicio nginx-lb, accedo al contenido de Nginx con el siguiente comando:

```sh

# curl http://<external-ip>:<port>
# donde <external-ip> es la dirección IP externa (LoadBalancer Ingress) de nginx-lb, y <port> es el valor de Port en la descripción del servicio nginx-lb.

$  curl 34.89.129.36:80

```


![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/service1.2.png  "Load Balancer Service")

Como estamos utilizando minikube, al escribir minikube service my-service se abrirá automáticamente el contenido de Nginx en un navegador.

```sh

$  minikube service nginx-lb

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/service1.3.png  "Load Balancer Service")

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/service1.4.png  "Load Balancer Service")


**2. De forma interna, sin acceso desde el exterior (crea service2.yaml)**

Para esto debemos crear un servicio de tipo ClusterIP

Configuramos nuestro yaml como se muestra a continuación:

**service2.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx-server
    tier: backend
spec:
  selector:
    tier: backend
  ports:
    - protocol: TCP
      port: 80
```

Esta especificación creará un servicio con target al puerto TCP 80 en cualquier Pod con la etiqueta app: nginx-server

```sh

$ kubectl create -f answer_exercise_3/service2.yaml  

$ kubectl get services

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/services2.0.png  "ClusterIP Service")

```sh

$ kubectl describe service nginx

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/services2.1.png  "ClusterIP Service")

Para verificar que mi servicio funciona correctamente, hago un curl de mi servicio nginx dentro de uno de mis pods...

```sh
$ kubectl get pods 

$ kubectl exec -it <podName> -- bash

```

```sh
#curl <CLUSTER-IP>:<PORT> 

$ curl 10.68.1.81:80

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/services2.2.png  "ClusterIP Service")

**3. Abriendo un puerto especifico de la VM (crea service3.yaml)**

Para esto debemos crear un servicio de tipo NodePort

Configuramos nuestro yaml como se muestra a continuación:

**service3.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-np
  labels:
    app: nginx-server
    tier: backend
spec:
  selector:
    tier: backend
  type: NodePort
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 80
      name: https
```

En el service3.yaml se configuraron los puertos **port y targetPort**, en donde **port**: es el puerto de servicio abstraído, que puede ser cualquier puerto que utilicen otros pods para acceder al servicio y **targetPort**: es el puerto en el que el contenedor acepta tráfico, para el contenedor de nginx es el puerto 80.

```sh

$ kubectl create -f answer_exercise_3/service3.yaml  

$ kubectl get services

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/services3.1.png  "NodePort Service 1")

```sh

$ kubectl describe service nginx-np

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/services3.2.png  "NodePort Service 2")

Para verificar que mi servicio funciona correctamente:

Hago un curl de mi servicio < MINIKUBE-IP >:< PORT >

```sh

$ minikube ip

$ curl 192.168.64.2:32763

```


![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/services3.3.png  "NodePort Service 3")