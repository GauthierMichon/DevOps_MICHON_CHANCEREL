# DevOps_MICHON_CHANCEREL

## 1.

On créer 2 namespaces que l'on appelle :

    - namespace-devops303
    - namespace-hello-world

`kubectl create namespace namespace-devops303`
`kubectl create namespace namespace-hello-world`

On applique les quota :

`kubectl apply -f devops303/namespaces-redis-devops303/devops303-quota.yml --namespace=namespace-devops303`
`kubectl apply -f hello-world/hello-world-quota.yml --namespace=namespace-hello-world`

## 2.

On commence par build notre image pour devops303