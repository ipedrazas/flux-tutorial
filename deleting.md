
# Deleting resources

Let's look at what happens when we delete resources. If you delete a `Pod` managed by a `Deployment`, the ReplicaSet 
controller will create a new `Pod`. How do we delete resources using Flux?

Let's try to delete our `grafana.yaml` release and see what happens. Go ahead, delete the file, commit, push and if 
you're not very patient, force the flux sync with the command: `fluxctl sync --k8s-fwd-ns flux`

Now check if you have your grafana release:

`helm ls`

As you can see the grafana release has not been deleted. This is because to allow Flux to delete resources we have to 
enable a flag, so, redeploy Flux using the following command:


```
helm upgrade -i flux \
--set helmOperator.create=true \
--set helmOperator.createCRD=false \
--set git.url=git@github.com:ipedrazas/flux-tutorial \
--set syncGarbageCollection.enabled=true \
--namespace flux \
weaveworks/flux
```
