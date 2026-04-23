# biarritzburo.com — Contexte projet

## Hébergement

- **Serveur** : VPS Hetzner avec cPanel
- **Username cPanel** : `biarritzburo`
- **Deploy path** : `/home/biarritzburo/public_html/`
- **Déploiement** : via `.cpanel.yml` (Git Version Control de cPanel, push sur `main` → déploiement auto)
- **Domaine** : https://biarritzburo.com (redirection www → sans-www, HTTPS forcé)

## Stack

- **HTML / CSS / JS vanilla** — pas de framework, pas de build step
- Pas de Node, pas de bundler, pas de dépendances
- Tout est servi statiquement par Apache

## Conventions

### Chemins
- **Chemins relatifs uniquement** pour les ressources locales (ex: `css/style.css`, pas `/css/style.css`). Le site doit fonctionner en ouverture directe `file://` et ne pas casser si déployé dans un sous-dossier.

### Images
- **WebP en priorité** pour les photos (fallback JPEG si besoin)
- **SVG inline** pour les icônes et logos (pas de `<img src="icon.svg">` sauf cas spécifique)
- **Jamais de hotlink** : toutes les images hébergées localement dans `assets/`
- **`alt` obligatoire** sur toutes les `<img>` (alt vide `alt=""` uniquement pour les images décoratives)

### Mobile-first
- CSS écrit en mobile-first (media queries `min-width` pour les tailles plus grandes)
- Viewport meta toujours présent
- Images responsive (`max-width: 100%; height: auto;` déjà dans le reset)

## SEO

- `<title>` unique et descriptif sur chaque page (50-60 caractères)
- `<meta name="description">` unique sur chaque page (150-160 caractères)
- Balises Open Graph complètes (og:title, og:description, og:image, og:url, og:type, og:locale)
- Twitter Card (`summary_large_image`)
- Schema.org (JSON-LD) sur les pages clés quand applicable
- `sitemap.xml` à maintenir à jour (ajouter chaque nouvelle page)
- `robots.txt` ouvert avec lien vers sitemap
- `<link rel="canonical">` sur chaque page

## Cache-busting

Le `.htaccess` impose un cache navigateur **1 mois** sur CSS/JS. Sans cache-busting, les visiteurs récurrents verraient du code périmé pendant un mois.

**Règle** : à chaque modification de `css/style.css` ou `js/main.js`, **bumper le query string** `?v=AAAAMMJJx` dans `index.html` et toutes les pages qui les référencent.

- Format : `?v=AAAAMMJJx` — date ISO (AAAAMMJJ) + lettre (`a`, `b`, `c`...) pour les modifs multiples dans la journée
- Exemple : `?v=20260423a` → après une 2e modif le même jour → `?v=20260423b`
- À appliquer sur **toutes les pages** qui référencent le fichier modifié

```html
<link rel="stylesheet" href="css/style.css?v=20260423a">
<script src="js/main.js?v=20260423a"></script>
```

## Git

- **`main` = production** — chaque push déclenche un déploiement via `.cpanel.yml`
- **Jamais de push direct sur `main`** pour les modifs non triviales : passer par une branche feature
- **Branches feature** : `feat/xxx`, `fix/xxx`, `docs/xxx`
- **Merge** : via PR review, pas de force push sur `main`
- **Commits** : messages clairs en français, impératif présent ("Ajoute la section contact", pas "Ajouté...")

## Fichiers sensibles

Le `.htaccess` bloque l'accès à :
- Tous les fichiers commençant par `.` (dotfiles)
- Les fichiers `.env`, `.log`, `.bak`, `.old`, `.swp`, `.md`, `.yml`, `.gitignore`
- Les dossiers `.claude/` et `.git/`

Ne jamais committer de secrets (API keys, mots de passe, tokens).
