kubectl create -f deployment.yaml
kubectl create -f service.yaml
kubectl autoscale deployment nginx --cpu-percent=50 --min=1 --max=6


kubectl get hpa

kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://nginx-svc; done"
