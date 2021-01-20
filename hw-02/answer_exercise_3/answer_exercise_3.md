# Kubernetes - Services
## Crea un objeto de tipo service para exponer la [aplicación](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_2) de las siguientes formas:

1. Exponiendo el servicio hacia el exterior (crea service1.yaml)

2. De forma interna, sin acceso desde el exterior (crea service2.yaml)

3. Abriendo un puerto especifico de la VM (crea service3.yaml)

## Desarrollo

**1. Exponiendo el servicio hacia el exterior (crea service1.yaml) LoadBalancer or Ingress Controller**

Para esto debemos crear un servicio de tipo Ingress

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
```sh

$ kubectl create -f answer_exercise_3/service1.yaml  

$ kubectl get services

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/service1.0.png  "Load Balancer Service")

Use the external IP address (LoadBalancer Ingress) to access the Hello World application:

curl http://<external-ip>:<port>
where <external-ip> is the external IP address (LoadBalancer Ingress) of your Service, and <port> is the value of Port in your Service description. If you are using minikube, typing minikube service my-service will automatically open the Hello World application in a browser.

Crear firewall rule
gcloud compute firewall-rules create test-node-port --allow tcp:node-port
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