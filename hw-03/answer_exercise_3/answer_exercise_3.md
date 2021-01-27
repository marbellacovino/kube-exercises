# Kubernetes - [Horizontal Pod Autoscaler] 

## Crea un objeto de kubernetes HPA, que escale a partir de las métricas CPU o memoria (a vuestra elección). Establece el umbral al 50% de CPU/memoria utilizada, cuando pase el umbral, automáticamente se deberá escalar al doble de replicas.


## Desarrollo:

A partir de un objeto de deployment de Nginx con las siguientes especificaciones:

    • Imagen: nginx

    • Version: 1.19.4

    • 3 replicas

    • Limits:
    CPU: 1 milicores Memoria: 128Mi

    • Requests:
    CPU: 1 milicores Memoria: 128Mi

Creo mi objeto de deployment, servicio y escalar automáticamente a 6 replicas cuando mi cpu=0.5m, es decir, al 50% de cpu utilizado:

```sh

kubectl create -f deployment.yaml
kubectl create -f service.yaml
kubectl autoscale deployment nginx --cpu-percent=50 --min=3 --max=6
kubectl get deployment nginx

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer3/autoscale1.0.png  "autoscale1")

Podemos verificar el estado actual del escalador automático ejecutando:

```sh

$kubectl get hpa

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer3/autoscale1.0.png  "autoscale1.0")


Ahora, iniciaremos un contenedor y enviaremos un bucle infinito de consultas al servicio de nginx para observar cómo reacciona el escalador automático al aumento de carga. En un terminal diferente ejecuto:

```sh

$kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://nginx-svc; done"

```
Y para ver el estado actual del escalador automático, ejecutamos en otro terminal:

```sh

$kubectl get hpa --watch

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer3/autoscale1.1.png  "autoscale1.1")

En unos minutos, deberíamos ver que la carga de CPU va incrementado:

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer3/autoscale1.1.png  "autoscale1.2")

El consumo de CPU ha superado el 50% de la solicitud. Como resultado, las replicas de mi deployment se escalaron a 6 réplicas:

```sh
$kubectl get pods --watch
$kubectl get deployment nginx

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer3/autoscale1.1.png  "autoscale1.3")