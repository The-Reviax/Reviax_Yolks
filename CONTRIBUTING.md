# Contribuer à Reviax Yolks

Images Docker (yolks) pour Pterodactyl. Les eggs JSON vivent dans un dépôt séparé (voir § Eggs).

## Branches

| Branche | Rôle | Publication GHCR |
|---|---|---|
| `main` | Production — utilisée par les eggs | `<famille>:<tag>` |
| `beta` | Pré-production — en test sur un serveur | `<famille>:<tag>-beta` |
| `alpha/<sujet>` | Travail en cours, peut casser | aucune |

- Flux : `alpha/<sujet>` → PR vers `beta` → test → PR `beta` → `main`.
- `<sujet>` en kebab-case, court : `alpha/ghcr-namespace`, `alpha/java-21`, `alpha/nginx-php83`.
- Jamais de push direct sur `main`.
- Une branche `alpha/` mergée se supprime.
- Les workflows publient depuis `main` et `beta` uniquement ; une PR ne fait qu'un build de vérification.
- Décision ouverte avant tout merge dans `main` : nom d'auteur dans LICENSE, en-têtes et `LABEL author`.

## Images

- Chemin : `<famille>/<tag>/Dockerfile` → `ghcr.io/the-reviax/<famille>:<tag>` (minuscules).
- Familles actuelles : `games`, `installers`, `nginx`. Un workflow par famille.
- Tag = ce qui distingue la variante : version (`php8.1`, `java21`) ou moteur/jeu (`source`). Exception historique : `nginx:main` = nginx sans PHP.
- Images d'exécution : utilisateur `container`, `HOME=/home/container`. Installers : root, sans entrypoint.
- Base épinglée sur une branche de version (ex. `alpine:3.19`, `bookworm-slim`) ; seule exception : `installers/alpine` en `alpine:latest`.
- Chaque Dockerfile porte `LABEL org.opencontainers.image.source="https://github.com/The-Reviax/Reviax_Yolks"`.
- Package GHCR neuf : le passer en Public après la première publication.

## Commits

Format : `type(portée): description` en français, à l'impératif. Portée = famille (`games`, `installers`, `nginx`) ou `repo`.

Types : `feat` (nouvelle image), `fix` (correction), `ci` (workflows), `docs`, `chore` (maintenance).

Exemple : `fix(nginx): crée l'utilisateur container`.

## Eggs

- Dépôt séparé `Reviax_Pterodactyl_Eggs` (à créer s'il n'existe pas) : `<categorie>/<jeu>/egg-<jeu>.json` (ex. `games/gmod/egg-gmod.json`).
- Auteur de l'egg : `administrator@reviax.fr`. Image : toujours une URL `ghcr.io/the-reviax/...`.
- Ce dépôt doit être renommé `Reviax_Pterodactyl_Yolks` (pas encore fait) ; GitHub redirige l'ancienne URL. Au renommage : mettre à jour les 6 `LABEL org.opencontainers.image.source`, ce fichier et le titre du README.
