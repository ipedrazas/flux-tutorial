# Automation

Continuous delivery can be achieved by enabling `Automation` in the Flux workload (resources that contain Pods). `Automation` can be enabled by issuing a flux command or adding an annotation to the workload::

 
```
   # Command
   fluxctl automate --workload=default:deployment/helloworld

   # Annotation 
   flux.weave.works/automated: "true"
```
 

In order for a release to be `automated` the `HelmRelease` object needs to have the anotation.

```
—
apiVersion: flux.weave.works/v1beta1
kind: HelmRelease
metadata:
  name: ambassador
  namespace: demo
  annotations:
    flux.weave.works/automated: "true"
    flux.weave.works/tag.workload: glob:0.70.*
    flux.weave.works/tag.opa: semver:~1.0
spec:
  releaseName: ramb01
  chart:
    repository: https://kubernetes-charts.storage.googleapis.com/
    name: ambassador
    version: 2.5.1
  values:
    adminService.type: NodePort
```

Because the element we want to test here is how a `docker push` will result in a new deployment in our cluster, we're going to create a web application based on nginx and deploy it using automation:

First let's create a file (`index.html`) containing a ver simple web page
```
<html>
<head>
</head>
<body>
<h1>Hello automation</h1>
</body>
</html>
```

Now we create a `Dockerfile`

```
FROM nginx
COPY index.html /usr/share/nginx/html
```

Build the Dockerfile:

```
docker build -t ipedrazas/automation-demo:test-v1 .
```

We push the new created image to docker hub:

```
docker push ipedrazas/automation-demo
```

So, what we want now is to create a Helm chart and a Helm release to deploy our application:

```
helm create myautomation
touch namespaces/demo/automation.yaml
```

in the automation chart we have to modify the docker image used by the chart in the values files, also we want to add an ingress to `automation.minikube.info`. This will be our new `values.yaml` (remember to update the docker image to point to your own docker hub user).

```
—
apiVersion: flux.weave.works/v1beta1
kind: HelmRelease
metadata:
  name: myautomation
  namespace: demo
  annotations:
    flux.weave.works/automated: "true"
    flux.weave.works/tag.workload: glob:test-*
    flux.weave.works/tag.opa: semver:~1.0
spec:
  releaseName: aut01
  chartGitPath: automation
    image:
      repository: ipedrazas/automation
      tag: test-v1
```
