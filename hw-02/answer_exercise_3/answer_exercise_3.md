# Kubernetes - Services
## Crea un objeto de tipo service para exponer la [aplicación](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_2) de las siguientes formas:

1. Exponiendo el servicio hacia el exterior (crea service1.yaml)

2. De forma interna, sin acceso desde el exterior (crea service2.yaml)

3. Abriendo un puerto especifico de la VM (crea service3.yaml)

## Desarrollo

**1. Exponiendo el servicio hacia el exterior (crea service1.yaml) LoadBalancer or Ingress Controller**

**2. De forma interna, sin acceso desde el exterior (crea service2.yaml)**

Para esto debemos crear un servicio de tipo ClusterIP

Configuramos nuestro yaml como se muestra a continuación:

**service2.yaml**

```yaml
kind: Service
metadata:
  name: nginx-server
spec:
  selector:
    app: nginx-server
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 80 
```

En el service2.yaml se configuraron los puertos **port y targetPort**, en donde **port**: es el puerto de servicio abstraído, que puede ser cualquier puerto que utilicen otros pods para acceder al servicio y **targetPort**: es el puerto en el que el contenedor acepta tráfico, para el contenedor de [nginx es el puerto 80](https://github.com/marbellacovino/kube-exercises/blob/main/README.md).
```sh

$ kubectl create -f answer_exercise_3/service2.yaml  

$ kubectl get services

```

**3. Abriendo un puerto especifico de la VM (crea service3.yaml)**

Para esto debemos crear un servicio de tipo NodePort

```sh

$ kubectl create -f answer_exercise_3/service3.yaml  

$ kubectl get services

```