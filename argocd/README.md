# ArgoCD

Depuis deux jours, nous avons pu découvrir une partie de l'écosystème Kubernetes et surtout découvrir une façon unique d'interagir avec des ressources matérielles.

L'approche déclarative est le fondement de Kubernetes et aussi de la pratique du GitOps.

Le courant GitOps propose de travailler [en respectant ces principes](https://github.com/open-gitops/documents/blob/v1.0.0/PRINCIPLES.md) 

- Notre système est entièrement décrit sous une forme déclarative
- L'état de notre système est versionné et immutable dans un dépôt GIT (immutable au sens qu'il n'est modifié qu'après une modification de la version en cours)
- L'état est tiré depuis le dépôt de référence de façon automatique et systématique
- Sur le système, des agents se charge de veiller à ce que l'état correspondant au dépôt soit respecté (réconciliation)

Grâce à notre Chart Helm et nos Custom Resources nous allons pouvoir décrire complètement notre application et la déposer dans un projet Git qui sera ensuite traité par notre contrôleur ArgoCD.

## Installation d'ArgoCD

Comme vous travaillez sur un cluster Kubernetes complet pour la production, tous les outils nécessaires sous forme d'opérateurs sont installés. Vous allez donc pouvoir déployer un service ArgCD, avec la CRD ArgoCD. Cette ressource va instancier les différentes briques : un controleur qui applique l'état souhaité, un controleur qui récupère l'état et un cache redis.

```
kubectl create -f argocd.yaml
```
