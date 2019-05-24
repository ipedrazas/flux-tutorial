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