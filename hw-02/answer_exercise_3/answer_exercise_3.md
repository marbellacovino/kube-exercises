# Kubernetes - Services
## Crea un objeto de tipo service para exponer la [aplicación](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_2) de las siguientes formas:

1. Exponiendo el servicio hacia el exterior (crea service1.yaml)

2. De forma interna, sin acceso desde el exterior (crea service2.yaml)

3. Abriendo un puerto especifico de la VM (crea service3.yaml)

## Desarrollo

**1. Exponiendo el servicio hacia el exterior (crea service1.yaml) LoadBalancer or Ingress Controller**

**2. De forma interna, sin acceso desde el exterior (crea service2.yaml)**

Para esto debemos crear un servicio de tipo ClusterIP

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