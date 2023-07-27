# wp-k8s
WordPress implementation on Kubernetes. Based on [the tutorial from Kubernetes' documentation](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/), but on real cluster (no Minikube).

[Simplest as possible](https://en.wikipedia.org/wiki/KISS_principle), to be useful as Kubernetes cluster deployment exercise/test.


## Preface

WordPress + MySQL configurations require the creation of a cluster with `PersistentVolumes` and `Secrets`. I'm also using [Kustomize](https://kustomize.io/) for the `Secret` generator.

### 1. Security first

Following the tutorial, the first step is adding a `Secrets` generator using a kustomization file. 

I generated a random password for MySQL: `GA3DIMJRGUZTSNZT`. For learning purposes, such values are exposed, but this should never be a practice (neither in practice environments).

The following code must be added to `kustomization.yml`:

```
secretGenerator:
- name: mysql-pass
  literals:
  - password=GA3DIMJRGUZTSNZT #arbitrarily random password
```

