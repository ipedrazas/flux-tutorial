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

## Connecting Flux to GitHub

Last step is to get the public key and add it to our Github repo. We run the following command:

```
kubectl -n flux logs deployment/flux | grep identity.pub | cut -d '"' -f2
```

It should return somethimng like :

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDFKKgzroap+6u8vQ/LgyradFTfYUbQ7ADLcb057lchiphR8twcxY7R5wpd1fUFL/Q4+r7ynjIsrCLw1N3M3Wgah/tVtyAW7q4VEE4ZjnTI6ieWMyN+PxymNf9R8PYRbeuq+K0Bh4JW7hvVeqlCBVkyM5A0HT7qNXokVXe2rABrlfp3kRX8l55v5Lc9Rc0yNrRoZXObwebAWM6Wq746LbJPqDttcsXdGVKZAz/w3Zd3mBH0GygbIgfUnyHPMxNhvrLpQKse3eB63or0eyFNIBFHppnuhZsI3DBsVlDWrLMHJQbbwe859MBTYDlGNilksmHcjS5RUyZUCx
```

Now, you have to go to GitHub and create your own Flux repo. Once the repo has been created, go to `Settings` in your Github repo, `Deploy Keys` and add a new key using the result from the last command. Make sure you check `Allow write access` checkbox to allow flux to tag releases.
 
 The deafult structure we are going to start with is:

```
-> %  tree
.
├── namespaces
├── releases
└── resources
```