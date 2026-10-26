# Contribuer

## Ajouter une application

1. Écrivez `packages/<id>/package.yaml`. L'identifiant est en minuscules, avec des tirets (`uptime-kuma`). Le plus simple :
   - partir d'une application proche ;
   - ou l'écrire dans l'éditeur de paquets de MyInfra, puis l'exporter en YAML.
2. Écrivez `packages/<id>/item.yaml` :

   ```yaml
   version: 1.0.0
   ```

3. Ajoutez l'identifiant dans `order.txt`, à la place où l'application doit apparaître.
4. Si son logo n'est pas dans [simple-icons](https://simpleicons.org), ajoutez `logos/<nom>.svg` (ou `.png`) et sa source dans `logos/SOURCES.md`.

## Ce qu'un paquet de la bibliothèque demande en plus

- `name`, `summary` et `category`. La catégorie est l'une de celles de `categories.yaml`.
- Un bloc `about` avec :
  - `site` : une adresse https ;
  - `logo` : le nom simple-icons, ou celui d'un fichier de `logos/` ;
  - `description` : 80 caractères au moins ;
  - `translations` : `summary` et `description` pour `en`, `pt`, `it` et `de`.
- Chaque question (`inputs`) doit être lue quelque part, avec `{{input:clé}}`.

## Variables

Les variables du paquet (`env`) sont chiffrées et données à tous les services.

- `{{secret:NOM}}` tire un mot de passe à l'installation. Le même nom donne le même mot de passe partout, par exemple pour la base et l'application.
- `{{url}}` et `{{host}}` suivent le domaine de l'application à chaque livraison.

## Ajouter un template

`templates/<id>/template.yaml` :

```yaml
type: myinfra-library-template
version: 1
name: Médiathèque
summary: Films, séries, musique et livres audio…
sizing: 4 Go de mémoire et beaucoup de disque
translations:
  en: { name: …, summary: …, sizing: … }
  pt: { … }
  it: { … }
  de: { … }
cluster: medias
servers:
  - name: medias-1
    controlPlane: true
apps:
  - package: jellyfin
  - package: navidrome
    subdomain: musique   # le nom de l'application, l'id du paquet par défaut
```

Un template ne cite que des paquets sans question (sans `inputs`), et un nombre impair de plans de contrôle.

## Partager dans la communauté

Un paquet ou un template qui n'a pas sa place dans la bibliothèque elle-même (une pile de plusieurs applications reliées, un usage plus rare) va dans `community/`, avec les mêmes fichiers et les mêmes règles :

```
community/order.txt                     les paquets de la communauté, dans l'ordre des écrans
community/packages/<id>/package.yaml
community/packages/<id>/item.yaml
community/templates/<id>/template.yaml
community/templates/<id>/item.yaml
community/templates/order.txt
```

L'identifiant ne doit pas déjà exister dans la bibliothèque. Un template de la communauté peut citer des paquets de la bibliothèque et de la communauté ; un template de la bibliothèque ne cite que ceux de la bibliothèque.

## Vérifier

La CI vérifie chaque PR avec les règles de MyInfra lui-même. En local, depuis un clone de MyInfra :

```sh
go run ./cmd/library check ../MyInfra-templates-and-packages-library
```

Pour voir la bibliothèque dans MyInfra avant une release, lancez le serveur de dev de MyInfra avec `MYINFRA_LIBRARY_DIR=/chemin/vers/ce/dépôt`.

## Modifier un élément

Montez sa version dans `item.yaml` (voir le README) et décrivez le changement dans `changes`. La CI refuse un élément modifié dont la version n'a pas monté.
