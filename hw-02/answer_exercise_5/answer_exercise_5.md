# Kubernetes - Blue Green Deployment
## Diseña una estrategia de despliegue que se base en ”Blue Green” con las especificaciones del [ejercicio 1](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_1).

## Desarrollo

Configuramos nuestro yaml como se muestra a continuación:

**deployment.yaml**

```yaml

```

Y creamos nuestro deployment con el siguiente comando:

```sh

$ kubectl apply -f answer_exercise_5/deployment-v1.yaml

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/deployment.png  "Deployment")

kubectl apply -f answer_exercise_5/service.yaml


```sh

$ kubectl apply -f answer_exercise_5/deployment-v2.yaml

```