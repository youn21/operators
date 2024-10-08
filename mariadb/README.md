# Déployer Mariadb

La CRD Mariadb offre [un grand nombre de paramètres](https://github.com/mariadb-operator/mariadb-operator/blob/main/examples/manifests/mariadb_full.yaml). Pour notre TP, nous allons nous baser sur [cet exemple](mariadb.yaml) :

```
kubeclt -f mariadb/mariadb.yaml
```

Maintenant, nous avons produit un StatefulSet avec une syntaxte plus compacte en utilisant notre Secret créé juste précédemment.

## Ajout d'un Backup

Une ressource standard équivalent à `cron` sous Unix est le [CronJob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/) que vous avez déjà utilisé dans le Chart Helm.

Avec la [CRD Backup](https://github.com/mariadb-operator/mariadb-operator/blob/main/examples/manifests/backup.yaml) de Mariadb, déployez une sauvegarde régulière de votre base de données.

Vérifiez que le CronJob est bien créé.

## Gérer la configuration MAriaDB via les CRD

L'opérateur MariaDB permet de faire évoluer votre instance MariaDB grâce une configuration déclarative (ajout d'une base, d'un utilisateur, de droits, etc.).

Vous pouvez tester avec [ces exemples](/mariadb/customize/)

De cette façon nous voyons que nous pouvons manipuler l'ensemble des ressources d'un service de base de données de manière exclusivement déclarative.

Nous allons maintenant voir comment ce paradigme de configuration est tout à fait adapté à la pratique du `GitOps` que nous allons découvrir maintenant avec [ArgoCD](/argocd)