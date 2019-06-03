# Helm

Flux can deploy Helm releases using the Helm operator. The Helm operator deals with Helm Chart releases. The operator watches for changes of Custom Resources of kind HelmRelease. It receives Kubernetes Events and acts accordingly, installing, upgrading or deleting a Chart release.

We have covered the installation of Helm in the [installing](installing.md) document 