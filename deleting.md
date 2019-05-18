
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