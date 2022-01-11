# DevOps_MICHON_CHANCEREL

## 1. Découpage des namespaces

On créer 2 namespaces que l'on appelle :

    - namespace-devops303
    - namespace-hello-world

`kubectl create namespace namespace-devops303`
`kubectl create namespace namespace-hello-world`

ça ressemble à ça :

```
$ kubectl get namespace
NAME                    STATUS   AGE
default                 Active   70d
ingress-nginx           Active   69d
kube-node-lease         Active   70d
kube-public             Active   70d
kube-system             Active   70d
namespace-devops303     Active   101m
namespace-hello-world   Active   101m
```

On applique les quota :

`kubectl apply -f devops303/namespaces-redis-devops303/devops303-quota.yml --namespace=namespace-devops303`
`kubectl apply -f hello-world/hello-world-quota.yml --namespace=namespace-hello-world`

## 2. Wordpress

On commence par build notre image pour devops303

Puis on crée nos ingress avec les commandes :
`kubectl apply -f devops303/namespaces-redis-devops303/devops303-ingress.yml`
`kubectl apply -f hello-world/hello-world-ingress.yml`

On crée notre configMap avec à l'intérieur les variables d'environnement : 

    - REDIS_HOST
    - REDIS_PORT
  

## 3. RBAC

On doit créer 3 servicesAccounts.
`kubectl apply -f devops303/namespaces-redis-devops303/ServiceAccount-client.yml`
`kubectl apply -f devops303/namespaces-redis-devops303/ServiceAccount-dev.yml`
`kubectl apply -f devops303/namespaces-redis-devops303/ServiceAccount-admin.yml`

ça ressemble à ça :

```
$ kubectl get serviceAccounts
NAME      SECRETS   AGE
admin     1         21s
client    1         49s
default   1         70d
dev       1         25s
```