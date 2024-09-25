# Les opérateurs

## Rappels

Les opérateurs sont un outillage qui se repose sur la possibilité d'enrichir les APIs et les ressources Kubernetes. [L'opérateur](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) est composé d'un couple [Custom Resource Definition](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) (CRD) et [controleur](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/#writing-operator)

Le CRD se présente sous la forme d'un schéma yaml, il a donc aussi la capacité à controler syntaxiquement la ressource. Ensuite, le controleur (qui est un Pod) scrute les nouvelles ressources de ce type de CRD et déclare des ressources standard de Kubernetes.

Les opérateurs sont là pour faciliter le déploiement d'applications ou de services complexes. Pour la suite de cette formation, nous allons explorer l'apport des opérateurs dans le cycle de vie des applications Kubernetes. Dans la suite de cette formation, nous utiliserons principalement les opérateurs suivants :
- MariaDB
- Argocd
- Prometheus
- Grafana

## MariaDB

Dans le TP précédent nous avons déployé un serveur MariaDB sous forme de StateFullSet et de Chart Helm. Ici nous allons remplacer cette instance par l'opérateur MariaDB déjà installé sur le cluster.

L'opérateur MariadDB permet déjà d'instancier un statefullSet comme vu précédement, mais aussi d'ajouter toutes sortes de fonctionnalité comme gérer les droits (Grants), les utilisateurs, les bases de données, les sauvegardes et les restauration, les cluster Galera, etc. en se basant sur le paradigme déclaratif de Kubernetes.

