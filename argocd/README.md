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

Comme vous travaillez sur un cluster Kubernetes complet pour la production, tous les outils nécessaires sous forme d'opérateurs sont installés. Vous allez donc pouvoir déployer un service ArgCD, avec la CRD ArgoCD. [Cette ressource](/argocd/argocd.yaml) va instancier les différentes briques : un controleur qui applique l'état souhaité, un controleur qui récupère l'état et un cache redis. La configuration proposée ne fait que réduire les ressources pour travailler dans un contexte mono projet (minimisation des ressources consommées).

```
kubectl create -f argocd.yaml
```

Les configurations de votre applications sont partagées en deux dossiers, un qui déploie suivant [un chart Helm](/argocd/app/helm) pour Grr et l'autre [suivant des manifests](/argocd/app/manifests) pour Mariadb et les Secret. Ce découpage est nécessaire car ArgoCD ne sais pas traiter ces deux modes de déploiements dans un même répertoire.

Les Secrets contiennent des informations sensibles, qui ne doivent pas être archivées dans un dépôt GIT, c'est pour celà que nous allons créer des SealedSecret, chiffrés, qui eux seront traduits en Secret dans notre instance.

## Déploiement de l'Application en GitOps

Maintenant nous avons la machinerie pour appliquer notre configuration en se basant sur un dépôt GIT de prêt. L'étape suivante est de clôner ce dépôt chez vous et de configurer ArgoCD pour y faire référence. Afin de vous simplifier la vie, clônez le dépôt et rendez-le **public**.

Ce que vous devez faire :

1) modifiez [les deux Applications ArgoCD](/argocd/app/app.yaml) pour pointer sur votre dépôt et corrigez le nom du namespace pour celui correspondant à votre projet Openshift
2) corrigez l'URL de l'Ingress dans les values du chart Helm
3) Créez un SealedSecret pour les secrets de connexion à MariaDB que vous déposez dans le dossier `manifests`

Quand cela est prêt, vous pouvez déployer votre application :

```
kubectl create -f app/app.yaml
```

Vous pouvez vous connecter à l'interface web de ArgoCD (retrouvez sa Route : `oc get routes`) en tant qu'admin avec le mot de passe stocké en base64 dans le secret `argocd-grr-secret`

Maintenant, vous n'accédez plus aux commandes `kubectl` ni la console web pour ajouter, supprimer ou modifier votre application. Vous travaillez dorénavant selon le modèle GitOps.

4) Ajoutez le Backup de votre base de données !