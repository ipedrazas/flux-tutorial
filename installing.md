# Installing Flux

## Installing Helm

Create the service account and clusterrolebinding

```
kubectl -n kube-system create sa tiller

kubectl create clusterrolebinding tiller-cluster-rule \
    --clusterrole=cluster-admin \
    --serviceaccount=kube-system:tiller
```

Deploy Tiller in kube-system namespace:

```
helm init --skip-refresh --upgrade --service-account tiller
```

## Installing Flux from a chart 

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
--set git.url=git@github.com:ipedrazas/flux-tutorial \
--namespace flux \
weaveworks/flux
```
