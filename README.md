# CryptoSécurité — Site web

Site statique de la plateforme d'audit de tokens Solana francophone.

## Fichiers

```
site/
├── index.html        ← Page principale (CSS + JS intégrés)
└── badge_list.json   ← Liste des tokens certifiés (chargée dynamiquement)
```

## Déploiement sur GitHub Pages

### 1. Créer le dépôt GitHub

```bash
git init
git remote add origin https://github.com/TON_USER/cryptosecurite-site.git
```

### 2. Pousser le dossier `site/`

```bash
git add site/
git commit -m "Initial site"
git push -u origin main
```

### 3. Activer GitHub Pages

1. Aller dans **Settings → Pages** du dépôt
2. Source : **Deploy from a branch**
3. Branch : `main` — dossier : `/site`
4. Sauvegarder → le site sera accessible sur `https://TON_USER.github.io/cryptosecurite-site/`

---

## Mise à jour de badge_list.json

Le bot écrit le fichier maître dans `C:\bot TRADe\logs\badge_list.json`.
Le site lit `./badge_list.json` dans ce dossier.

### Option A — Copie manuelle

Copier après chaque badge accordé :

```bash
copy "C:\bot TRADe\logs\badge_list.json" "C:\bot TRADe\site\badge_list.json"
git -C "C:\bot TRADe\site" add badge_list.json
git -C "C:\bot TRADe\site" commit -m "Update tokens"
git -C "C:\bot TRADe\site" push
```

### Option B — Script de sync automatique

Créer `sync_site.bat` dans `C:\bot TRADe\` :

```bat
@echo off
copy /Y "logs\badge_list.json" "site\badge_list.json"
cd site
git add badge_list.json
git commit -m "Sync tokens %date% %time%"
git push
```

Planifier avec le Planificateur de tâches Windows toutes les heures.

### Option C — GitHub Actions (recommandé)

Ajouter `.github/workflows/sync.yml` dans le dépôt :

```yaml
name: Sync badge list

on:
  push:
    paths:
      - 'site/badge_list.json'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with:
          path: site/
      - uses: actions/deploy-pages@v4
```

---

## Développement local

Ouvrir avec un serveur local pour que `fetch('./badge_list.json')` fonctionne :

```bash
# Python
python -m http.server 8080 --directory site

# Node
npx serve site
```

Puis ouvrir `http://localhost:8080`.

> **Note :** Ouvrir `index.html` directement via `file://` bloque le fetch (CORS).
> Le site affiche alors "Aucun token certifié" — c'est normal en local sans serveur.
