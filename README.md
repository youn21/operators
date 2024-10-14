# Les opérateurs

## Rappels

Les opérateurs constituent un outillage qui se repose sur la possibilité d'enrichir les APIs et les ressources Kubernetes. [L'opérateur](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) est composé d'un couple [Custom Resource Definition](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) (CRD) et [controleur](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/#writing-operator)

Le CRD contient un schéma yaml, il a donc aussi la capacité de controler syntaxiquement la ressource. Ensuite, le controleur (qui est un Pod) scrute les nouvelles ressources correspondant à ce type de CRD et en déduit des ressources de base de Kubernetes (Pod, Deployement, StatefulSet, Serice, Ingress, etc.). L'opérateur a pour object de proposer une boîte à outil permettant de déployer et superviser un système complexe en se basant sur une déclaration la plus simple et compacte possible. L'utilisateur n'ayant pas à se soucier de multiples déclarations qui doivent interopérer.

Pour la suite de cette formation, nous allons explorer l'apport des opérateurs dans le cycle de vie des applications Kubernetes. Aujourd'hui nous découvrirons les opérateurs suivants :
- [KubeSeal](/kubeseal) (que vous avez déjà utilisé pour chiffrer les secrets)
- [MariaDB](/mariadb)
- [Argocd](/argocd)

Et demain nous verrons :

- le PRA
- Prometheus
- Grafana

Retrouvez la [présentation en markdown](PRESENTATION.md) ou en mode présentation sur [codimd](https://codimd.math.cnrs.fr/p/t1ca2RATq#/)
