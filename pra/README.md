# Mettre en oeuvre un PRA dans le Cloud

De manière générale, considérons que travailler dans le Cloud ne nous protège pas de la perte des données, bien au contraire... Considérons donc que notre cluster Kubernetes ne doit pas être le seul lieu pour contenir nos données. Il faut donc que ces données soient copiées ailleurs, tout le temps.

Pour cela, notre application doit impérativement être accompagnée d'un système qui copie les données ailleurs (ici notre base de données MariaDB). Et naturellement, nous allons nous appuyer sur un stockage en tant que service qui est le stockage S3 (initialement développé par Amazon, accessible via le protocole HTTPS).

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

- Créez un autre projet et à l'intérieur créez votre ObjectBucketClaim selon l'exemple ci-dessus.
- Vous obtiendrez les informations de connexion à votre stockage en interrogeant le Secret et la ConfigMap du nom de votre OBC.
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

Maintenant, vous pouvez détruire votre projet, le récréer, installez votre ArgoCD et c'est reparti !