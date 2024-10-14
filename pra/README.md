# Mettre en oeuvre un PRA dans le Cloud

De manière générale, considérons que travailler dans le Cloud ne nous protège pas de la perte des données, bien au contraire... Considérons donc que notre cluster Kubernetes ne doit pas être le seul lieu pour contenir nos données. Il faut donc que ces données soient copiées ailleurs, tout le temps.

Pour cela, notre application doit impérativement être accompagnée d'un système qui sauvegarde les données hors de mon Cloud (ici notre base de données MariaDB). Et naturellement, nous allons nous appuyer sur un stockage en tant que service qui est le stockage S3 (initialement développé par Amazon, accessible via le protocole HTTPS).

Pour les besoins de notre TP, le *ailleurs* sera simplement un autre namespace/projet sur ce cluster Openshift.

Là encore, votre Cloud a été configuré pour réclamer du stockage S3 à la demande via une Custom Resource !

Voici un exemple de demande de stockage S3 qu'on nommme `ObjectBucketClaim` (OBC) :

```
kind: ObjectBucketClaim
apiVersion: objectbucket.io/v1alpha1
metadata:
  name: pra
spec:
  additionalConfig:
    bucketclass: local-bucketclass
    maxSize: 1G
  generateBucketName: pra
  storageClassName: local-storage.noobaa.io
```

## Mise en place de notre PRA

- Créez un autre projet et à l'intérieur créez votre ObjectBucketClaim selon l'exemple ci-dessus.
- Vous obtiendrez les informations de connexion à votre stockage en interrogeant le Secret et la ConfigMap du nom de votre OBC.
- Créez une ressource SealedSecret reprenant les secrets de connexion à votre Bucket S3 et récupérez le nom de votre bucket dans la configMap 
- Modifiez votre Backup (via votre dépôt GIT ArgoCD) pour [backuper sur un stockage S3](https://github.com/mariadb-operator/mariadb-operator/blob/main/examples/manifests/backup_s3.yaml). 

Voici un exemple de configuration du stockage S3 que vous pouvez utiliser :

```
apiVersion: k8s.mariadb.com/v1alpha1
kind: Backup
metadata:
  name: pra-s3
spec:
... # Votre conf de backup précédente
  storage:
    s3:
      accessKeyIdSecretKeyRef:
        key: AWS_ACCESS_KEY_ID
        name: pra
      bucket: pra-62ceec3d-XXXXXXXXXXXXXXXXXXX
      endpoint: 's3-openshift-storage.apps.anf.math.cnrs.fr:443'
      prefix: mariadb
      secretAccessKeySecretKeyRef:
        key: AWS_SECRET_ACCESS_KEY
        name: pra
      tls:
        enabled: true
```

Déposez dans votre dossier `manifests` ArgoCD le SealedSecret et la ressource Backup...

- Maintenant, toujours grâce à l'opérateur MariaDB, adaptez juste votre instance MariaDB [pour démarrer sur le dernier Backup](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/BACKUP.md#bootstrap-new-mariadb-instances) en ajoutant la rubrique `bootstrapFrom`.

Maintenant, vous pouvez détruire votre projet, le récréer, redéployez `argocd.yaml` et `app.yaml` depuis votre dossier `argocd` et c'est reparti !

## Des opérateurs utiles pour un PRA

[Velero](https://velero.io/) est un projet complet de backup et restauration d'applications Kubernetes, son utilisation est pertinente pour la sauvegarde de l'ensemble d'un cluster Kubernetes, donc d'un point de vue infrastructure.

[Volsync](https://volsync.readthedocs.io/en/stable/) est un bon candidat pour mettre en place de la sauvegarde des données persistantes hors de votre cluster. Volsync met à disposition différentes méthodes de copies des données (restic, rclone, rsync via ssh).
Nous allons utiliser une fonctionnalité de la gestion des stockages sur Kubernetes, c'est à dire la création de VolumeSnapshift (snapshift de système de fichier), de notre stockage S3 *distant* et de la fonctionnalité [rclone de Volsync](https://volsync.readthedocs.io/en/stable/usage/rclone/index.html).

Nous avons besoin de créer un Secret reprenant l'authentification S3 du Secret pra :

```
kind: Secret
apiVersion: v1
metadata:
  name: rclone-secret
type: Opaque
stringData:
  [rclone-bucket]
  type = s3
  provider = Rclone
  env_auth = false
  access_key_id = ACCESS_KEY
  secret_access_key = SECRET_KEY
  endpoint = https://s3-openshift-storage.apps.anf.math.cnrs.fr
```

Et ensuite d'un CRD de type [ReplicationSource](https://volsync.readthedocs.io/en/stable/usage/rclone/index.html#source-configuration) :

```
apiVersion: volsync.backube/v1alpha1
kind: ReplicationSource
metadata:
  name: volsync
spec:
  rclone:
    copyMethod: Snapshot
    rcloneConfig: rclone-secret
    rcloneConfigSection: rclone-bucket
    rcloneDestPath: pra-62ceec3d-XXXXXXXXXXXXX/mysql-pv-claim
    volumeSnapshotClassName: standard-csi-true
  sourcePVC: storage-mariadb-0
  trigger:
    schedule: '*/3 * * * *'
```

Ici nous mentionnons un *volumeSnapshotClassName* qui définit [un paramètre particulier](https://plmlab.math.cnrs.fr/plmteam/okd-clusters/anf/-/blob/main/openshift-config/storageclass/snapshot-class-adp.yaml?ref_type=heads#L12) qui permet à Kubernetes de demander un VolumeSnapshot même si le PVC en question est utilisé.

Il est possible de récupérer les données grâce à un [ReplicationDestination](https://volsync.readthedocs.io/en/stable/usage/rclone/index.html#destination-configuration)