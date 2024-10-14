---
type: slide
slideOptions:
  transition: slide
  theme: solarized
---
<style>
    section {
        text-align: left;
        font-size: 28px;
    }
    code {
        font-size: 18px;
    }
</style>

# Les opérateurs dans Kubernetes

ANF Mathrice

CIRM Luminy / Le 16 octobre 2024

[regardez-moi ici](https://codimd.math.cnrs.fr/p/t1ca2RATq#/) ou copiez-collez moi dans codimd...

---

## Introduction

- Kubernetes met à disposition une API permettant de créer des ressources, chacune répondant à une syntaxte précise (une Resource Definition) ;
- chaque ressource créée est gérée par un contrôleur (un Pod fonctionnant dans le cluster K8s) qui veille à ce que l'état définit par la ressource soit toujours appliqué (réconciliation)

---

## Exemple de ressource

``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

---

## Les Custom Resources Definition

Kubernetes permet détendre l'API native en déclarant de nouvelles ressources à travers les Custom Resources Definitions (CRD)

``` yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: shirts.stable.example.com
spec:
  group: stable.example.com
  scope: Namespaced
  names:
    plural: shirts
    singular: shirt
    kind: Shirt
  versions:
  - name: v1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            properties:
              color:
                type: string
              size:
                type: string
    selectableFields:
    - jsonPath: .spec.color
    - jsonPath: .spec.size
    additionalPrinterColumns:
    - jsonPath: .spec.color
      name: Color
      type: string
    - jsonPath: .spec.size
      name: Size
      type: string
```

---

## Les contrôleurs

- A partir de ce moment là, il est possible de créer des ressources répondant aux spécifications de ce CRD ;

- En tant que tel, ajouter des ressources répondant à un CRD ne provoque aucun changement dans le cluster K8s...

- Il faut associer à ces ressources un contrôleur qui va scruter ces configurations et exécuter un certain traitement. Comme par exemple créer des Pod, des ConfigMap, Secrets, Deployments, StateFulSets, etc.

---

## Les opérateurs

Les opérateurs sont un couple CRD/Contrôleur qui permettent d'ajouter dans votre cluster Kubernetes la configuration et la gestion d'un service plus complexe.

[Différents SDK](https://sdk.operatorframework.io/) existent pour fabriquer des opérateurs (Ansible, Helm, Go, ...)

---

## Les opérateurs

![](https://www.sokube.io/wp-content/uploads/013-b-operators.png)
(cf : https://www.sokube.io/blog/introduction-to-kubernetes-operators-1)

---

## Les opérateurs et moi ? (moi : le Dev)

Votre équipe en charge d'administrer le cluster Kubernetes peut installer des opérateurs pertinants pour l'ensemble des utilisateurs, vous pouvez aussi installer un opérateur dans un namespace/projet.

Dans le cadre de cette formation, l'administrateur a bien fait le travail... tout est installé...
