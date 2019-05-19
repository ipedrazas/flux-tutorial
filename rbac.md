# RBAC

How to manage RBAC is a very interesting question because the problem it's not how to define the manifest or what it should or should not have. The issue is who's the owner or who's responsible for it.

Here we are going to see an example of an application that needs cluster wide access. The application, [KStatus](https://github.com/ipedrazas/kstatus) it's a simple FLASK applicarion that queries the API Server to check the results of jobs with a certain set of labels.

In order to deploy this application, we're going to create a new directory called `status` and we will add all the resouces for the application that we want deployed in that namespace.

```
mkdir status
touch status/namespace.yaml
touch status/sa.yaml
touch status/release.yaml
```
The namespace is just a namespace definition:

```
apiVersion: v1
kind: Namespace
metadata:
  labels:
    origin: flux
  name: status
```

The `sa.yaml` is the service account defintion. We want this application to be run as a particular user because we will have to give certain extra permissions to the application. In Kubernetes we achieve this by creating:

* **ServiceAccount**: Internal user
* **Role**: what can you do (which endpoints of the API Server you're allowed to query)
* **RoleBinding**: binding between a `ServiceAccount` and a `Role`

The `Role`, or in this case, the `ClusterRole` for this application is the following:

```
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: kstatus-watch
  labels:
    origin: flux
rules:
- apiGroups: ["batch"]
  resources: ["jobs"]
  verbs: ["get", "list", "watch"]
```

As we can see we have defined a ClusterRole that can `get, list and watch` jobs. Bindings between a user and this role will allow that user to execute queries like:

```
kubectl get jobs --all-namespaces
```

In fact, the query we're doing in the application is the following:

```
kubectl get jobs --all-namespaces -l target=kstatus
```

I think it's important to note that the difference between this `ClusterRole` and the equivalent `Role` namespaced is that the endpoint of the API is different. For example, what's the difference between doing:


```
kubectl get pods -n demo
```

and

```
kubectl get pods -A
# The `-A` flag is to query all namespaces.
```

In the first case, we're calling the following endpoint:

```
-> %  curl localhost:8001/api/v1/namespaces/demo/pods
```

In the second, we're calling

```
-> %  curl localhost:8001/api/v1/pods
```

(To enable the local proxy to the API server you can execute `kubectl proxy` in a different terminal).

As you can see, these are two very different endpoints. It makes sense to have two different resources (Role, ClusterRole) to define what access is needed for each endpoint.

Hopefully now you will understand better what's the difference between a ClusterRole and a Role. One is in charge of defining the access to a namespaced endpoint (Role - /api/v1/namespaces/demo/pods), and the other one, more generic, is in charge of defining the access to a cluster wide endpoint (ClusterRole - /api/v1/pods).

You can see that unless your application is a Cluster application, an application that provides a cluster wide service, it should not use a not namespaced API call, or endpoint (not namespaced API calls need a ClusterRole).

So, now that we have seen the difference between Roles and ClusterRoles, let's look at how to define the different RBAC rules. To run `KStatus` we need to query `/apis/batch/v1/jobs`, as you can see, this API endpoint is not namespaced, so, we cannot add the resources to our namespace directory. What we are going to do is to create another directory in the root of the project called `rbac`:

```
mkdir rbac
touch rbac/clusterrole.yaml
touch rbac/clusterrolebinding.yaml
```

The content of those files are:

```
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: kstatus-watch
  labels:
    origin: flux
    owner: kstatus
rules:
- apiGroups: ["batch"]
  resources: ["jobs"]
  verbs: ["get", "list", "watch"]
```

And

```
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: kstatus-watch
  labels:
    origin: flux
    owner: kstatus
subjects:
- kind: ServiceAccount
  name: kstatus
  namespace: status
roleRef:
  kind: ClusterRole
  name: kstatus-watch
  apiGroup: rbac.authorization.k8s.io
```

Let's take a look at our repo structure:

```
-> %  tree
.
├── namespaces
│   ├── demo
│   │   ├── ambassador.yaml
│   │   ├── backend
│   │   │   └── postgresql.yaml
│   │   ├── demo.yaml
│   │   └── redis.yaml
│   └── status
│       ├── namespace.yaml
│       └── rbac
│           └── sa.yaml
├── rbac
│   ├── clusterrole.yaml
│   └── clusterrolebinding.yaml
└── rbac.md
```

We can see that 

