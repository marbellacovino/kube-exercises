# Kubernetes - [Ingress Controller / Secrets]

## [Ingress Controller / Secrets] Crea los siguientes objetos de forma declarativa con las siguientes especificaciones:

    • Imagen: nginx

    • Version: 1.19.4

    • 3 replicas

    • Label: app: nginx-server

    • Exponer el puerto 80 de los pods

    • Limits:
    CPU: 20 milicores Memoria: 128Mi

    • Requests:
    CPU: 20 milicores Memoria: 128Mi

1. A continuación, tras haber expuesto el servicio en el puerto 80, se deberá acceder a la página principal de Nginx a través de la siguiente URL:
http://<student_name>.student.lasalle.com

2. Una vez realizadas las pruebas con el protocolo HTTP, se pide acceder al servicio mediante la utilización del protocolo HTTPS, para ello:
• Crear un certificado mediante la herramienta OpenSSL u otra similar
• Crear un secret que contenga el certificado

## Desarrollo parte 1:

### Paso 1:

Una vez he configurado los yaml de deployment, ingress y service...los creo como se muestra a continuación:

```sh

  $kubectl create -f deployment.yaml

  $kubectl create -f service.yaml

  $kubectl create -f ingress.yaml

  $kubectl get all

  $kubectl get ingress

```

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer1/ingress1.0.png  "Ingress 1.0")

### Paso 2:

Configurar mi ip y dns, agregando 192.168.64.2 marbella.student.lasalle.nip.io al final del archivo /etc/hosts:

```sh

  $sudo nano etc/hosts

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer1/ingress1.2.png  "Ingress 1.2")

Esto envía solicitudes desde marbella.student.lasalle.nip.io a Minikube.

### Paso 3:

Verificar que el controlador de Ingress esta dirigiendo el tráfico a través de la siguiente URL:
http://marbella.student.lasalle.nip.io

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer1/ingress1.3.png  "Ingress 1.3")

## Desarrollo parte 2: TLS

### Paso 1: 

Crear un certificado mediante la herramienta OpenSSL:

```sh

  $openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:4096 -keyout private.key -out certificate.crt

```
![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer1/ingress1.4.png  "Ingress 1.4")

### Paso 2:

Una vez tengo mi certificado y llave privada debo crear un secret.yaml para guardar estos datos sensibles.

**secret.yaml**

```yaml

apiVersion: v1
kind: Secret
metadata:
  name: nginx-tls
  namespace: default
data:
  tls.crt: base64 encoded cert
  tls.key: base64 encoded key
type: kubernetes.io/tls


```

En donde **tls.crt** y **tls.key** deben estar codificados en base64, para esto ejecuto lo siguiente:

```sh

$cat certificate.crt | base64
$cat private.key | base64

```

Ahora copio y pego cada pieza de datos codificados en base64 en las secciones apropiadas del archivo secret.yaml

### Paso 3:

Configurar tls en el ingress agregando la siguiente configuración:

```yaml

tls:
  - hosts:
      - marbella.student.lasalle.nip.io
    secretName: nginx-tls
    
```

En donde **secretName** es el nombre que le asigne a mi secret.yaml

**ingress-tls.yaml**

```yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress-tls
spec:
  tls:
  - hosts:
      - marbella.student.lasalle.nip.io
    secretName: nginx-tls
  rules:
  - host: marbella.student.lasalle.nip.io
    http:
      paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: nginx-svc
                port:
                  number: 80

```
### Paso 4:

Ejecutar en la linea de comandos de kubectl:

```consola

  $kubectl delete ingress nginx-ingress

  $kubectl create -f secret.yaml

  $kubectl create -f ingress-tls.yaml

```
### Paso 5:

Verificar que el controlador de Ingress esta dirigiendo el tráfico a través de la siguiente URL:
http://marbella.student.lasalle.nip.io

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer1/ingress1.5.png  "Ingress 1.5")

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer1/ingress1.6.png  "Ingress 1.6")

![Alt text](https://github.com/marbellacovino/kube-exercises/blob/main/hw-03/images/answer1/ingress1.7.png  "Ingress 1.7")