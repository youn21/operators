# Les opérateurs

## Rappels

Les opérateurs sont un outillage qui se repose sur la possibilité d'enrichir les APIs et les ressources Kubernetes. [L'opérateur](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) est composé d'un couple [Custom Resource Definition](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) (CRD) et [controleur](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/#writing-operator)

Le CRD contient un schéma yaml, il a donc aussi la capacité à controler syntaxiquement la ressource. Ensuite, le controleur (qui est un Pod) scrute les nouvelles ressources correspondant à ce type de CRD et en déduit des ressources standard de Kubernetes (Pod, Deployement, StatefulSet, Serice, Ingress, etc.). L'opérateur a pour object de proposer une boîte à outil permettant de déployer et superviser un système complexe en se basant sur une déclaration la plus simple et compacte possible. L'utilisateur n'ayant pas à se soucier de multiple déclarations qui doivent interopérer.

Pour la suite de cette formation, nous allons explorer l'apport des opérateurs dans le cycle de vie des applications Kubernetes. Aujourd'hui nous découvrirons les opérateurs suivants :
- KubeSeal
- MariaDB
- Argocd

Et demain nous verrons :

- Prometheus
- Grafana

# KubeSeal

KubeSeal est un opérateur qui met à disposition une nouvelle ressource (CRD) à disposition de l'utilisateur. Cette ressource permet de créer un objet de type `SealedSecret` contenant des informations chiffrées par une clé asymétrique stockée dans le cluster Kubernetes à travers l'API Kubernetes. Ensuite, en appliquant la ressource `SealedSecret` créée dans le cluster, ce dernier le convertira en `Secret` qui sera alors disponible pour les applicatiosn (Deployments, StateFulSet, etc.). Et cet ressource `SealedSecret` peut-être exposée dans un dépôt Git par exemple sans compromettre des informations protégées (mot de passe par exemple).

Pour créer un `SealedSecret`, nous allons déjà créer une ressource de type `Secret` en ligne de commande sans l'appliquer au cluster (en mode `dry-run`) :

```
kubectl create secret generic --dry-run=client  mariadb-password --from-literal=mariadb-password=supersecret --from-literal=mariadb-root-password=topsecret -o yaml
```

Ensuite, nous allons produire une version chiffrée par le cluster :

```
kubectl create secret generic --dry-run=client  mariadb-password --from-literal=mariadb-password=supersecret --from-literal=mariadb-root-password=topsecret -o yaml |kubeseal -o yaml
```

Et enfin, nous allons l'envoyer dans notre cluster :

```
kubectl create secret generic --dry-run=client  mariadb-password --from-literal=mariadb-password=supersecret --from-literal=mariadb-root-password=topsecret -o yaml |kubeseal -o yaml | kubectl create -f -
```

Je peux maintenant afficher les ressources `SealedSecret` et `Secret` ainsi générées :

```
kubectl get sealedsecret -o yaml
kubectl get secret mariadb-password -o yaml
```

Et maintenant poursuivons avec [MariadDB](mariadb)