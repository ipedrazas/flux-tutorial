# Installing Flux

Add the Flux repository of Weaveworks:

```
helm repo add weaveworks https://weaveworks.github.io/flux
```

Apply the Helm Release CRD:
```
kubectl apply -f https://raw.githubusercontent.com/weaveworks/flux/master/deploy-helm/flux-helm-release-crd.yaml
```

Install Flux using the helm chart

```
helm upgrade -i flux \
--set helmOperator.create=true \
--set helmOperator.createCRD=false \
--set git.url=git@github.com:YOURUSER/flux-get-started \
--namespace flux \
weaveworks/flux
```
