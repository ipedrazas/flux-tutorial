
# Deleting resources

Let's look at what happens when we delete resources. If you delete a `Pod` managed by a `Deployment`, the ReplicaSet 
controller will create a new `Pod`. How do we delete resources using Flux?

First thing we have to do is to make sure that the `syncGarbageCollection.enabled` is set to `true`:

```
helm upgrade -i flux \
--set helmOperator.create=true \
--set helmOperator.createCRD=false \
--set git.url=git@github.com:ipedrazas/flux-tutorial \
--set syncGarbageCollection.enabled=true \
--namespace flux \
weaveworks/flux
```
Now, let's install a release, for example, Redis. We add the followin release:

```
apiVersion: flux.weave.works/v1beta1
kind: HelmRelease
metadata:
  name: redis
  namespace: demo
spec:
  releaseName: redis01
  chart:
    repository: https://kubernetes-charts.storage.googleapis.com/
    name: redis
    version: 7.1.1
```

Now, let's commit the release and see how Flux will deploy our Redis pods (1 master, 2 slaves).

```
-> %  helm ls
NAME   	REVISION	UPDATED                 	STATUS  	CHART        	APP VERSION	NAMESPACE
flux   	2       	Sat May 18 10:57:19 2019	DEPLOYED	flux-0.9.4   	1.12.2     	flux
redis01	1       	Sat May 18 11:04:25 2019	DEPLOYED	redis-7.1.1  	4.0.14     	demo
```

Once we have the Redis release installed, let's delete it and see what happens. Let's comment out the redis release and push the code.

As you can see, Flux has deleted the redis release.

Let's uncomment it and re-add it so Flux will redeploy. If we look at the logs we can see what's going on:

```
ts=2019-05-18T10:47:43.937942173Z caller=loop.go:103 component=sync-loop event=refreshed url=git@github.com:ipedrazas/flux-tutorial branch=master HEAD=270980d89035790c48ba73ffbdd8038c725e5096
ts=2019-05-18T10:47:46.794935005Z caller=sync.go:470 component=cluster method=Sync cmd=apply args= count=3
ts=2019-05-18T10:47:46.9798741Z caller=sync.go:536 component=cluster method=Sync cmd="kubectl apply -f -" took=184.830085ms err=null output="namespace/demo configured\nhelmrelease.flux.weave.works/ambassador unchanged\nhelmrelease.flux.weave.works/redis created"
ts=2019-05-18T10:47:51.704607399Z caller=daemon.go:624 component=daemon event="Sync: 270980d, demo:helmrelease/redis" logupstream=false
ts=2019-05-18T10:47:54.818049068Z caller=loop.go:441 component=sync-loop tag=flux-sync old=0bc10d6b28576c3f690ac82fe396ea5440b2a95b new=270980d89035790c48ba73ffbdd8038c725e5096
```