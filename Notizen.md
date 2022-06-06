# Horizontal Pod Autoscaling

**ACHTUNG!**

Scheinbar gibt es einen [Bug in Minikube](https://github.com/kubernetes/minikube/issues/13969), der probleme mit den Metriken verursacht. Deswegen muss Minikube mit zusätzlicher Config gestartet werden.
```
minikube start --extra-config=kubelet.housekeeping-interval=10s
```

[Anleitung](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/).

Wir haben zum testen zuerst mit 10 Replicas gearbeitet. Weil das einwandfrei funktioniert hat, sind wir auf 2 runter gegangen um Resourcen zu sparen.

Um HPA in minikube nutzen zu können, muss der metrics-server aktiviert werden. Das geht in Minikube über ein Addon.
Um das zu aktivieren braucht es den folgenden Befehl:
```
minikube addons enable metrics-server
```
Danach muss der metrics-server noch zur Nutzung ausgerollt werden. Dafür ist der Folgende Befehl nötig:
```
kubectl -n kube-system rollout status deployment metrics-server
```
Kubernetes bringt "von Haus aus" nur CPU und Memory Metriken mit. Für weitere Metriken kann bspw. der
[Prometheus-Adapter](https://github.com/kubernetes-sigs/prometheus-adapter) verwendet werden.