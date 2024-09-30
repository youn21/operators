# Déployer Mariadb

La CRD Mariadb offre [un grand nombre de paramètres](https://github.com/mariadb-operator/mariadb-operator/blob/main/examples/manifests/mariadb_full.yaml). Pour notre TP, nous allons nous baser sur [cet exemple](mariadb.yaml) :

```
kubeclt -f mariadb/mariadb.yaml
```

Maintenant, nous avons produit un StafulSet avec une syntaxte plus compacte en utilisant notre Secret créé juste au dessus.