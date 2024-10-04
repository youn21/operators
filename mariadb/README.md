# Déployer Mariadb

La CRD Mariadb offre [un grand nombre de paramètres](https://github.com/mariadb-operator/mariadb-operator/blob/main/examples/manifests/mariadb_full.yaml). Pour notre TP, nous allons nous baser sur [cet exemple](mariadb.yaml) :

```
kubeclt -f mariadb/mariadb.yaml
```

Maintenant, nous avons produit un StatefulSet avec une syntaxte plus compacte en utilisant notre Secret créé juste précédemment.

L'opérateur MariaDB permet de faire évoluer votre instance MariaDB grâce une configuration déclarative (ajout d'une base, d'un utilisateur, de droits, etc.). Ce paradigme de configuration déclarative est tout à fait adapté à la pratique du `GitOps` que nous allons découvrir maintenant avec [ArgoCD](/argocd)