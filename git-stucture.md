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


Once the sync has finished we can check if the helm released has been created:

```
-> %  helm ls
NAME   	REVISION	UPDATED                 	STATUS  	CHART           	APP VERSION	NAMESPACE
flux   	2       	Sat May 18 10:57:19 2019	DEPLOYED	flux-0.9.4      	1.12.2     	flux
posql01	1       	Sat May 18 11:55:33 2019	DEPLOYED	postgresql-5.0.0	11.3.0     	demo
ramb01 	1       	Sat May 18 11:39:48 2019	DEPLOYED	ambassador-2.5.1	0.61.1     	demo
redis01	1       	Sat May 18 11:47:47 2019	DEPLOYED	redis-7.1.1     	4.0.14     	demo
```

And as we can see, it has been created