# Kubernetes - Services
## Crea un objeto de tipo service para exponer la [aplicación](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_2) de las siguientes formas:

• Exponiendo el servicio hacia el exterior (crea service1.yaml)

• De forma interna, sin acceso desde el exterior (crea service2.yaml)

• Abriendo un puerto especifico de la VM (crea service3.yaml)

## Desarrollo

**• Exponiendo el servicio hacia el exterior (crea service1.yaml) LoadBalancer or Ingress Controller**

**• De forma interna, sin acceso desde el exterior (crea service2.yaml)**

Para esto debemos crear un servicio de tipo ClusterIP


```sh

$ kubectl create -f answer_exercise_3/service2.yaml  

$ kubectl get services

```

**• Abriendo un puerto especifico de la VM (crea service3.yaml)**

Para esto debemos crear un servicio de tipo NodePort

```sh

$ kubectl create -f answer_exercise_3/service3.yaml  

$ kubectl get services

```