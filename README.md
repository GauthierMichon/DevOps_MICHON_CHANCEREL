# DevOps_MICHON_CHANCEREL

## 1. Découpage des namespaces

Tout d'abord, on crée 2 namespaces que l'on appelle :

    - namespace-devops303
    - namespace-hello-world

Pour faire cela, on exécute les commandes suivantes : 

`kubectl create namespace namespace-devops303`<br>
`kubectl create namespace namespace-hello-world`

Dans namespace-devops303 nous auront tous les fichier de config pour lancer devops303 et redis.
Dans namespace-hello-world nous auront tous les fichier de config pour lancer hello-world.

En exécutant la commande `kubectl get namespace`, nous obtenons ceci :

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

On applique les quotas à nos namespaces.
Pour faire cela, on exécute les commandes suivantes : 

`kubectl apply -f devops303/namespaces-redis-devops303/devops303-quota.yml --namespace=namespace-devops303`<br>
`kubectl apply -f hello-world/hello-world-quota.yml --namespace=namespace-hello-world`

## 2. Mise en place du systeme devops303, redis et affichage de hello world

On commence par build notre image pour devops303

Puis on crée nos ingress avec les commandes suivantes :

`kubectl apply -f devops303/namespaces-redis-devops303/devops303-ingress.yml`<br>
`kubectl apply -f hello-world/hello-world-ingress.yml`

En exécutant la commande `kubectl get ingress`, nous obtenons ceci :
```
$ kubectl get ingress
NAME             CLASS   HOSTS                  ADDRESS     PORTS   AGE
devops303        nginx   devops303.gautjam      localhost   80      178m
hello-world-tp   nginx   hello-world.gautjam    localhost   80      4h54m
```

On crée notre configMap avec à l'intérieur les variables d'environnement : 

    - REDIS_HOST
    - REDIS_PORT

Voici le fichier de configuration de notre configMap :
```
kind: Service
apiVersion: v1
metadata:
  name: service-redis-devops303
spec: 
  selector:
    app: redis-devops303
  ports:
    - protocol: TCP
      port: 6379 
      targetPort: 6379
```

Pour le metadata name, on met le nom que l'on a donné à notre service redis.


## 3. RBAC

On doit créer 3 servicesAccounts : 

`kubectl apply -f devops303/namespaces-redis-devops303/ServiceAccount-client.yml`<br>
`kubectl apply -f devops303/namespaces-redis-devops303/ServiceAccount-dev.yml`<br>
`kubectl apply -f devops303/namespaces-redis-devops303/ServiceAccount-admin.yml`

En exécutant la commande `kubectl get serviceAccounts`, nous obtenons ceci :

```
$ kubectl get serviceAccounts
NAME      SECRETS   AGE
admin     1         21s
client    1         49s
default   1         70d
dev       1         25s
```

Pour la création de role, j'ai utilisé cette documentation : https://kubernetes.io/docs/reference/access-authn-authz/rbac/

On crée nos roles client, admin et dev.
Exemple de config pour le role client : 

```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: role-client
  namespace: default
rules:
- apiGroups: [""]
  resources: ["pods", "port-forward"]
  verbs: ["get", "watch", "list"]
```

## 4. Monitoring

Tout d'abord, il nous helm : https://helm.sh/

Ensuite on excécute la commande :
`helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`

Il nous faut installer grafana : https://grafana.com/grafana/download?platform=linux

Une fois télécharger, on peut aller sur http://localhost:3000 qui est l'adresse sur laquelle écoute Grafana de base

Maintenant pour créer la data source Prometheus, on va sur l'icone de paramètre, puis sur data source.
Add Data Source et choisir Prometheus.
On choisi notre URL, ici http://devops303.gautjam