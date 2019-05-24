# GitOPS

Gitops is an operating model for Kubernetes and cloud native.  It provides a set of best practices to join up deployment, management and monitoring for containerized clusters and applications.  It's a path towards a **developer centric** experience for managing applications.  

It means applying the Git workflow to operations, as well as development.  Note that this is not just about Git push, it’s about how we set up the entire CI and CD toolchain and UI/UX. 


## A concise description of GitOps

1. Describe the desired state of the whole system using a declarative specification for each environment.  (In our story, Bob’s team owns the whole system config in Git.)

    * A git repo is the single source of truth for the desired state of the whole system. 
    * All changes to the desired state are Git commits.
    * All specified properties of the cluster are also observable in the cluster, so that we can detect if the desired and observed states are the same (converged) or different (diverged).   

2. When the desired and observed states are not the same then:

    * There is a convergence mechanism to bring the desired and observed states in sync both eventually, and autonomically. Within the cluster, this is Kubernetes.
    * This is triggered immediately with a “change committed” alert.  
    * After a configurable interval, an alert “diff” may also be sent if the states are divergent.

3. Hence all Git commits cause verifiable and idempotent updates in the cluster. 

    * Rollback is: “convergence to an earlier desired state”.
4. Convergence is eventual and indicated by:

    * No more “diff” alerts during a defined time interval.
    * A “converged” alert (eg. webhook, Git writeback event).


## What is divergence?

To repeat: All specified properties of the cluster are also observable in the cluster. 

Some examples of divergence:

* A change in a configuration file, due to a merged update in Git.
* A change in a configuration file, due to the GUI effecting a commit to Git.
* Multiple changes in the desired state due to a merged PR to Git followed by a container image build and subsequent config updates.
* The observed state changes in the cluster due to an error, resource conflicts causing poor behavior, or just random “drift” from the original state.
