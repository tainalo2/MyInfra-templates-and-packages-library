# Bibliothèque MyInfra

Les paquets d'applications et les templates que [MyInfra](https://github.com/tainalo2/MyInfra) propose dans sa **Bibliothèque**. Ils sont publiés à part du moteur : une application s'ajoute ou se corrige sans attendre une version de MyInfra.

Chaque installation de MyInfra connaît ce dépôt. Toutes les 6 heures, elle cherche la dernière release, vérifie sa signature et propose son contenu. Hors ligne, elle s'appuie sur la release intégrée à son binaire.

## Contenu

```
categories.yaml               les catégories, dans l'ordre des écrans, traduites
order.txt                     les paquets, dans l'ordre des écrans
packages/<id>/package.yaml    un paquet MyInfra (type: myinfra-package)
packages/<id>/item.yaml       sa version
templates/<id>/template.yaml  un cluster et des paquets qui vont bien ensemble
templates/<id>/item.yaml      sa version
logos/<nom>.svg|png           les logos que simple-icons n'a pas (voir logos/SOURCES.md)
```

- **Un paquet** a le même format que ceux de l'éditeur de paquets de MyInfra (Applications > Paquets). Le guide de cet éditeur décrit chaque champ.
- **Un template** (`type: myinfra-library-template`) donne un cluster, ses serveurs et des paquets de la bibliothèque. Il ne contient ni fournisseur, ni offre, ni clé : chacun les choisit en prenant le template.

## Versions

- **Chaque paquet et chaque template a sa version** (`item.yaml`, de la forme `1.2.0`). Quand vous modifiez un élément, montez sa version :
  - **patch** pour une nouvelle image ou un texte corrigé ;
  - **minor** pour un service, une variable ou un disque en plus ;
  - **major** pour un changement qui demande d'intervenir sur les données.
- **`changes`** dit en une phrase ce que la version apporte. MyInfra l'affiche sur sa page Mises à jour.
- **`minMyInfra`** donne la version de MyInfra la plus ancienne qui sait lire l'élément (un champ de paquet récent, par exemple). Une version plus ancienne l'ignore.
- **Le tag d'une release** (`v1.4.0`) versionne l'ensemble. Une release contient la dernière version de chaque élément.
- **Un identifiant publié ne change plus et ne disparaît pas** : les templates et les copies des utilisateurs y font référence.

Une copie dans MyInfra (un paquet copié dans « Paquets », un template pris dans « Templates ») garde sa version. MyInfra propose la suivante sur sa page Mises à jour. Les applications déjà installées ne changent pas.

## Ce qui reste dans MyInfra

Les profils de sauvegarde et de redondance (commandes de dump et de restauration de certaines applications) sont du comportement du moteur. Ils restent dans MyInfra (`internal/market/redundancy.go`) et ne dépendent pas du réseau.

## Contribuer

Voir [CONTRIBUTING.md](CONTRIBUTING.md).

## Releases

Une release est publiée à chaque tag `v*`. Le workflow `release.yml` :

1. vérifie la bibliothèque et compare les versions à la release précédente ;
2. écrit `library.tar.gz` (avec `index.json`, l'empreinte de chaque fichier) et `SHA256SUMS` ;
3. signe `SHA256SUMS` avec la clé ed25519 du secret `LIBRARY_SIGNING_KEY`, ce qui donne `SHA256SUMS.sig`.

MyInfra n'accepte qu'une release signée par une clé qu'il connaît (`internal/library/current.go`). Pour changer de clé, ajoutez d'abord la nouvelle clé publique dans une version de MyInfra, puis signez avec elle.
