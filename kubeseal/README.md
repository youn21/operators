# KubeSeal

KubeSeal est un opérateur qui met à disposition une nouvelle ressource (CRD) à disposition de l'utilisateur. Cette ressource permet de créer un objet de type `SealedSecret` contenant des informations chiffrées par une clé asymétrique stockée dans le cluster Kubernetes à travers l'API Kubernetes. Ensuite, en appliquant la ressource `SealedSecret` créée dans le cluster, ce dernier le convertira en `Secret` qui sera alors disponible pour les applicatiosn (Deployments, StateFulSet, etc.). Et cet ressource `SealedSecret` peut-être exposée dans un dépôt Git par exemple sans compromettre des informations protégées (mot de passe par exemple).

Pour créer un `SealedSecret`, nous allons déjà créer une ressource de type `Secret` en ligne de commande sans l'appliquer au cluster (en mode `dry-run`) :

```
kubectl create secret generic --dry-run=client  mariadb-password --from-literal=DB_NAME=grr --from-literal=DB_USER=grr_user --from-literal=DB_PASSWORD=supersecret -o yaml
```

Ensuite, nous allons produire une version chiffrée par le cluster :

```
kubectl create secret generic --dry-run=client  mariadb-password --from-literal=DB_NAME=grr --from-literal=DB_USER=grr_user --from-literal=DB_PASSWORD=supersecret  -o yaml |kubeseal -o yaml
```

Et enfin, nous allons l'envoyer dans notre cluster :

```
kubectl create secret generic --dry-run=client  mariadb-password --from-literal=DB_NAME=grr --from-literal=DB_USER=grr_user --from-literal=DB_PASSWORD=supersecret -o yaml |kubeseal -o yaml | kubectl create -f -
```

Je peux maintenant afficher les ressources `SealedSecret` et `Secret` ainsi générées :

```
kubectl get sealedsecret -o yaml
kubectl get secret mariadb-password -o yaml
```

Et maintenant poursuivons avec [MariadDB](/mariadb)