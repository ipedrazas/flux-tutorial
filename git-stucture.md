# Git Structure

Once you start managing applications using Flux you will want to add some structure into your repo. This is totally possible, since Flux doesn't care about the structure.

Let's create a directory called `backend and let's add a `Postgres` database:

```
apiVersion: flux.weave.works/v1beta1
kind: HelmRelease
metadata:
  name: postgresql
  namespace: demo
spec:
  releaseName: posql01
  chart:
    repository: https://kubernetes-charts.storage.googleapis.com/
    name: postgresql
    version: 5.0.0
```

Let's push and sync

```
git add .
git commit -m "adding backend dir with postgres"
fluxctl sync --k8s-fwd-ns flux
```


