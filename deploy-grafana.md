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
