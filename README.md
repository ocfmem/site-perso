# Build & déploiement (hébergement statique)

## Pré-requis

- Hugo Extended installé (`hugo version`)

## Développement local

```bash
hugo server
```

Site local : `http://localhost:1313`

---

## Build de production (domaine final)

Le domaine de prod est configuré dans `hugo.production.toml` :

- `baseURL = 'https://www.meddy-menzikoff.fr/'`

Commande de build :

```bash
hugo --config hugo.toml,hugo.production.toml --minify
```

Le site généré est dans le dossier `public/`.

---

## Déployer sur un hébergement statique

1. Lancer le build de prod
2. Uploader le contenu de `public/` sur l’hébergement
3. Configurer le domaine `www.meddy-menzikoff.fr`

> Important : publier le **contenu** de `public/` (pas le dossier parent autour).

---

## Vérification rapide avant mise en ligne

```bash
hugo --config hugo.toml,hugo.production.toml --minify
```

Puis vérifier dans `public/index.html` que les URLs pointent bien vers :

- `https://www.meddy-menzikoff.fr/...`
