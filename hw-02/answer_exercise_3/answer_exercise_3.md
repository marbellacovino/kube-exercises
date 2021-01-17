• Exponiendo el servicio hacia el exterior (crea service1.yaml) LoadBalancer or Ingress Controller
• De forma interna, sin acceso desde el exterior (crea service2.yaml) ClusterIP
2.  kubectl create -f answer_exercise_3/service2.yaml   
• Abriendo un puerto especifico de la VM (crea service3.yaml) NodePort
3. kubectl create -f answer_exercise_3/service3.yaml

