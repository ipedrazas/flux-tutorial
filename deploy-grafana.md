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

Let's customise our grafana installation by adding an ingress rule:
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