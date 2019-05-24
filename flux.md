# Flux

Flux is a tool that automatically ensures that the state of a cluster matches the config in git. It uses an operator in the cluster to trigger deployments inside Kubernetes, which means you don't need a separate CD tool. It monitors all relevant image repositories, detects new images, triggers deployments and updates the desired running configuration based on that (and a configurable policy).

The benefits are: you don't need to grant your CI access to the cluster, every change is atomic and transactional, git has your audit log. Each transaction either fails or succeeds cleanly. You're entirely code centric and don't need new infrastructure.

## Automated deployment of new container images

Another feature is the automated deployment of containers. It will continuously monitor a range of container registries and deploy new versions where applicable.

This is really useful for keeping the repository and therefore the cluster up to date. It allows separate teams to have their own deployment pipelines since Flux is able to see the new image and update the cluster accordingly.

This feature can be disabled and images can be locked to a specific version.

## Integrations with other devops tools

One final high-level feature is that Flux increases visibility of your application. Clear visibility of the state of a cluster is key for maintaining operational systems. Developers can be confident in their changes by observing a predictable series of deployment events.