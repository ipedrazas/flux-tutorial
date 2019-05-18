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

And as we can see, it has been created. This means that we can define our own directory structure to manage our applications. For example, let's refactor opur git repo and add a directory with the namespace name and all the resources we want to add into it:

```
-> %  tree
.
├── demo
│   ├── ambassador.yaml
│   ├── backend
│   │   └── postgresql.yaml
│   ├── demo.yaml
│   └── redis.yaml
```

Let's commit this change and see what happens:

```
-> %  git push
Enumerating objects: 8, done.
Counting objects: 100% (8/8), done.
Delta compression using up to 8 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 863 bytes | 863.00 KiB/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 3 local objects.
To github.com:ipedrazas/flux-tutorial.git
   08674e9..207cbd3  master -> master
```

And now let's check Flux's logs to see what happened with our releases:

```
s=2019-05-18T11:02:08.960055411Z caller=loop.go:103 component=sync-loop event=refreshed url=git@github.com:ipedrazas/flux-tutorial branch=master HEAD=207cbd356433b80cf91fca43f13b2fdc987a4ef3
ts=2019-05-18T11:02:11.875381042Z caller=sync.go:470 component=cluster method=Sync cmd=apply args= count=4
ts=2019-05-18T11:02:13.087807767Z caller=sync.go:536 component=cluster method=Sync cmd="kubectl apply -f -" took=1.212285708s err=null output="namespace/demo configured\nhelmrelease.flux.weave.works/ambassador unchanged\nhelmrelease.flux.weave.works/postgresql unchanged\nhelmrelease.flux.weave.works/redis unchanged"
ts=2019-05-18T11:02:16.701795684Z caller=daemon.go:624 component=daemon event="Sync: 207cbd3, <cluster>:namespace/demo, demo:helmrelease/ambassador, demo:helmrelease/postgresql, demo:helmrelease/redis" logupstream=false
t
```

In particular the following output:

```
namespace/demo configured
helmrelease.flux.weave.works/ambassador unchanged
helmrelease.flux.weave.works/postgresql unchanged
helmrelease.flux.weave.works/redis unchanged
```

As we can see, changing the directory structure has no impact on our application releases.