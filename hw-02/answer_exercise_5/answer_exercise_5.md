# Kubernetes - Blue Green Deployment
## Diseña una estrategia de despliegue que se base en ”Blue Green” con las especificaciones del [ejercicio 1](https://github.com/marbellacovino/kube-exercises/tree/main/hw-02/answer_exercise_1).

## Desarrollo

Configuramos nuestro yaml como se muestra a continuación:

**deployment.yaml**

```yaml

```

Y creamos nuestro deployment con el siguiente comando:

Desde el directorio answer_exercise_5

```sh

$ kubectl apply -f deployment-v1.yaml
$ kubectl apply -f deployment-v2.yaml

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-02/images/deployment.png  "Deployment")

```sh

$ kubectl apply -f service.yaml

```

 kubectl patch service nginx-bluegreensvc -p '{"spec":{"selector":{"app":"nginx-green","version":"v2.0.0"}}}'

kubectl delete -f deployment-v1.yaml