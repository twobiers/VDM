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

# Secruity

* Minikube verwendet standardmäßig X.509 Zertifikate, die unter `$HOME/.minikube` liegen. Diese werden automatisch generiert, lassen sich aber auch anpassen
  * Nach [OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Kubernetes_Security_Cheat_Sheet.html#api-authentication) eigenen sich X.509 Zertifikate für kleine, nicht-produktive Cluster. Die Authentifizierung werden wir daher fürs erste dabei belassen
* Um Network Policies in minikube verwenden zu können, muss ein CNI registriert werden, weil minikube per default keinen mitbringt. Dafür muss ein zusätzlicher Paremter beim Starten übergeben werden: `minikube start --cni calico`. So wird minikube Calico als CNI konfigurieren
  * Zum Testen der Network Policies wird ein BusyBox container verwendet: `kubectl run busybox --rm -it --image busybox /bin/sh`
    * Test kann dann bspw. mit folgendem Befehl passieren: `wget -q --timeout=5 http://openproject-web:8080 && echo "Successful" || echo "Not Successful"`
  * Erst Globale Richtlinie für Zero-Trust.