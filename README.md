# Reviax Yolks

Images Docker (« yolks ») pour Pterodactyl. Les **eggs** (fichiers JSON importés dans le panel) pointent vers ces images via leur champ `docker_images`.

## Structure

```
<famille>/<tag>/Dockerfile   →   ghcr.io/the-reviax/<famille>:<tag>
```

Une famille = un dossier racine = un workflow `.github/workflows/<famille>.yml`.

Les images marquées « à venir » seront publiées au premier build sur `main`.

| Famille | Tag | Image | Base | Archi |
|---|---|---|---|---|
| games | source | `ghcr.io/the-reviax/games:source` (à venir) | debian:bookworm-slim | amd64 |
| installers | alpine | `ghcr.io/the-reviax/installers:alpine` | alpine:latest | amd64, arm64 |
| installers | debian | `ghcr.io/the-reviax/installers:debian` | debian:bookworm-slim | amd64 |
| nginx | main | `ghcr.io/the-reviax/nginx:main` | alpine:3.24 | amd64, arm64 |
| nginx | php7.4 | `ghcr.io/the-reviax/nginx:php7.4` (à venir) | alpine:3.15 | amd64, arm64 |
| nginx | php8.1 | `ghcr.io/the-reviax/nginx:php8.1` (à venir) | alpine:3.19 | amd64, arm64 |

Notes :
- `nginx:main` = nginx seul, sans PHP (le tag ne désigne pas la branche `main`).
- `php7.4` et `php8.1` sont des versions PHP en fin de vie : Alpine est épinglée sur la dernière branche qui les fournit encore (3.15 pour php7, 3.19 pour php81). Ces bases ne reçoivent plus de correctifs de sécurité.
- `games:source` reste sur bookworm : `libncurses5:i386` n'existe plus dans trixie.
- `installers:alpine` suit volontairement `alpine:latest` : image jetable, utilisée seulement pendant l'installation d'un serveur.
- Les images nginx lancent nginx (et php-fpm) avec leur configuration dans `/home/container` ; la commande STARTUP de l'egg n'est pas exécutée.

## Publication

| Événement | Résultat |
|---|---|
| Pull request | Build de vérification, rien n'est publié |
| Merge sur `main` (ou build hebdomadaire du lundi) | `<famille>:<tag>` |
| Merge sur `beta` | `<famille>:<tag>-beta` |
| Autre branche | Rien |

Après la **première** publication d'un package, le passer en **Public** sur GitHub (Packages → package → Package settings → Change visibility) : Wings télécharge les images sans identifiants.

## Ajouter une image

1. Créer `<famille>/<tag>/Dockerfile` (+ `entrypoint.sh` pour une image d'exécution), avec l'utilisateur `container` et `HOME=/home/container`. Les installers tournent en root, sans entrypoint.
2. Ajouter `<tag>` à la matrice du workflow de la famille.
3. Depuis une branche `alpha/<sujet>`, ouvrir une PR vers `beta` et la merger, tester l'image `-beta`, puis ouvrir une PR `beta` → `main` et la merger.

## Utilisation dans un egg

```json
"docker_images": {
  "Source": "ghcr.io/the-reviax/games:source"
}
```

Script d'installation : `ghcr.io/the-reviax/installers:debian` (dépendances de SteamCMD) ou `ghcr.io/the-reviax/installers:alpine` (téléchargements simples).

## Contribuer

Conventions de branches, de commits et de nommage : [CONTRIBUTING.md](CONTRIBUTING.md).
