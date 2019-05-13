# Deploying a Helm chart using Helm

Create a namespace in the namespaces directory:

```
apiVersion: v1
kind: Namespace
metadata:
  labels:
    origin: flux
  name: demo
```

Now, add the `HelmRelease` object to the `releases` directory:

```
---
apiVersion: flux.weave.works/v1beta1
kind: HelmRelease
metadata:
  name: grafana
  namespace: demo
spec:
  releaseName: grafana
  chart:
    repository: https://kubernetes-charts.storage.googleapis.com/
    name: grafana
    version: 3.3.7
```

By doing this, we have achieved the same thing as executing the command `helm install stable/grafana`

Let's customise our grafana installation by adding an ingress rule to our grafana release file:
---
apiVersion: flux.weave.works/v1beta1
kind: HelmRelease
metadata:
  name: grafana
  namespace: demo
spec:
  releaseName: grafana
  chart:
    repository: https://kubernetes-charts.storage.googleapis.com/
    name: grafana
    version: 3.3.7
  values:
    service:
      type: NodePort
    ingress:
    hosts:
      - grafana.minikube.info

Let's grab the IP of the service:

```
minikube service grafana -n demo
```

To make `grafana.minikube.info` to resolve to the minikube service, we have to modify the `/etc/hosts` file adding the following lines:

```
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##

192.168.99.100	minikube.info
192.168.99.100  grafana.minikube.info
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
```

