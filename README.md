# wp-k8s
WordPress implementation on Kubernetes. Based on [the tutorial from Kubernetes' documentation](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/), but on real cluster (no Minikube).

[Simplest as possible](https://en.wikipedia.org/wiki/KISS_principle), to be useful as Kubernetes cluster deployment exercise/test.


## Preface

WordPress + MySQL configurations require the creation of a cluster with `PersistentVolumes` and `Secrets`. I'm also using [Kustomize](https://kustomize.io/) for the `Secret` generator.

### 1. Security first

Following the tutorial, the first step is adding a `Secrets` generator using a kustomization file. 

I generated a random password for MySQL: `GA3DIMJRGUZTSNZT`. For learning purposes, such values are exposed, but this should never be a practice (neither in practice environments).

The following code must be added to `kustomization.yaml`:

```
#kustomization.yaml
secretGenerator:
- name: mysql-pass
  literals:
  - password=GA3DIMJRGUZTSNZT #arbitrarily random password
```

### 2. MySQL 🐬 deployment

This MySQL container image is specifically configured for WordPress usage, which makes connections easier. It also mounts the `PersistentVolume` at `/var/lib/mysql`. The `MYSQL_ROOT_PASSWORD` environment variable sets the database password from the `Secret`.

It also contains a `Service` and a `Deployment`, where further data is declared.

### 3. WordPress 🅦 settings

The WordPress container mounts the `PersistentVolume` at `/var/www/html` for website data files. The `WORDPRESS_DB_HOST` environment variable sets the name of the MySQL `Service` defined above, and WordPress will access the database by `Service`. The `WORDPRESS_DB_PASSWORD` environment variable sets the database password from the `Secret` kustomize generated.

### 4. Back to Kustomization

After defining both `mysql.yaml` and `wordpress.yaml` files, those should be referenced on `kustomization.yaml`:

```
#kustomization.yaml
resources:
  - mysql.yaml
  - wordpress.yaml
```