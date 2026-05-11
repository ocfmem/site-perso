# Site personnel — Hugo

Site statique propulsé par **Hugo Extended** avec le thème `terminal`.

- Version par défaut : **Français**
- Version anglaise : **/en/**
- Domaine de production : **https://www.meddy-menzikoff.fr/**

---

## Pré-requis

- Hugo Extended installé

```bash
hugo version
```

---

## Développement local

Lancer le serveur local :

```bash
hugo server
```

Site local : `http://localhost:1313`

---

## Build production

La configuration de prod est dans `hugo.production.toml` :

- `baseURL = 'https://www.meddy-menzikoff.fr/'`

Commande de build :

```bash
hugo --config hugo.toml,hugo.production.toml --minify --gc
```

Le site généré est dans :

```text
public/
```

---

## Déploiement GitHub Pages

Le workflow est déjà présent :

```text
.github/workflows/hugo.yml
```

Il :
1. installe Hugo Extended,
2. build le site,
3. publie `public/` sur GitHub Pages.

### Configuration GitHub requise

Dans le repo GitHub :

- **Settings > Pages**
- Source : **GitHub Actions**

---

## Domaine personnalisé

Le domaine est défini via :

```text
static/CNAME
```

Contenu actuel :

```text
www.meddy-menzikoff.fr
```

Pour que le domaine fonctionne, configurer aussi les DNS (Route53) côté infrastructure.

---

## Fichiers importants

- `hugo.toml` : configuration principale (menus, langues, SEO global)
- `hugo.production.toml` : surcharge prod (`baseURL`)
- `content/` : pages et articles
- `layouts/partials/extended_head.html` : JSON-LD / SEO
- `layouts/partials/footer.html` : lien RSS en pied de page
- `.github/workflows/hugo.yml` : CI/CD GitHub Pages

---

## Vérification rapide avant mise en ligne

```bash
hugo --config hugo.toml,hugo.production.toml --minify --gc
```

Puis vérifier :

- `public/index.html`
- `public/sitemap.xml`
- `public/CNAME`
