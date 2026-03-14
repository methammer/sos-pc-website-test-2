# SOS-PC — Handoff Document
## Branche : `design-alternative`
## Repo : `github.com/methammer/sos-pc-website-test-2`
## Date : Mars 2026

---

## Contexte du projet

**Site principal** : https://sos-pc.click  
**Preview branche** : https://design-alternative--sos-pc-website-test-2.netlify.app/  
**Repo site** : https://github.com/methammer/sos-pc-website-test-2  
**Repo diagnostic** : https://github.com/methammer/sos-pc-diagnostic  
**Hébergement** : Netlify (deux sites séparés)

SOS-PC est un auto-entrepreneur en informatique basé à Le Castellet (04700).  
Services : réparation PC, création web, hébergement streaming, hébergement IA (coming soon).

---

## Architecture des deux repos

### `sos-pc-website-test-2` — Le site principal
```
src/
  layouts/Layout.astro       — Layout global + chatbot widget flottant v7
  pages/index.astro          — Page principale (design-alternative)
  pages/diagnostic.astro     — Page diagnostic standalone (branche main)
  pages/contact-success.astro
  components/
    Navbar.astro              — Navbar Tokyo Night avec hamburger mobile
    CoverPage.astro           — Cover page "swipe to reveal" (inchangée depuis main)
    AsciiBackground.astro     — Background ASCII animé
    ThemeSwitcher.astro       — Toggle dark/light
    LottieAnimation.astro     — Wrapper animations Lottie
public/
  diag.ps1 → redirect vers sos-pc-diagnostic.netlify.app/diag.ps1
netlify.toml                  — Redirects dont /diag.ps1
```

### `sos-pc-diagnostic` — Les Netlify Functions
```
netlify/functions/
  analyze.js    — POST /api/analyze  — Appel Claude API pour analyse PC
  chat.js       — POST /api/chat     — Chat IA avec contexte diagnostic
  collect.js    — POST /api/collect  — Stockage données via Netlify Blobs
  poll.js       — GET  /api/poll?s=  — Polling pour récupérer données
public/
  diag.ps1     — Script PowerShell collecte données PC
package.json   — Dépendance @netlify/blobs
```

---

## Design alternatif — Tokyo Night Theme

### Palette CSS (définie dans Layout.astro `:root`)
```css
--bg: #1a1b26          /* Fond principal */
--bg-card: #24283b     /* Cartes */
--bg-elevated: #2a2e42 /* Éléments surélevés */
--accent-hex: #00d4aa  /* Teal — couleur principale */
--accent: 0, 212, 170  /* Pour rgba() */
--text: #c0caf5        /* Texte principal */
--text-muted: #7982a9  /* Texte secondaire */
--border: #3b4261      /* Bordures */
--yellow: #e0af68      /* Avertissements */
--red: #f7768e         /* Erreurs/critique */
--blue: #7aa2f7        /* Info */
--purple: #bb9af7      /* Accent secondaire */
```

### Fonts Google (chargées dans Layout.astro `<head>`)
- `JetBrains Mono` — éléments tech, labels, code, monospace
- `Outfit` — corps de texte, titres

### Inspiré de Trash Guides (https://trash-guides.info)
- Grid pattern subtil en background (CSS `body::before`)
- Tags avec bordures colorées (`.tag-teal`, `.tag-yellow`, `.tag-blue`, etc.)
- Terminal mock dans le hero et la section diagnostic
- Cursor clignotant animé

---

## Chatbot widget flottant v7

**Fichier** : inline dans `src/layouts/Layout.astro`  
**Fonctionnement** :

1. Génère un session ID aléatoire (`$s='XXXXXXXX'`)
2. Affiche la commande : `$s='XXXXXXXX'; irm https://sos-pc.click/diag.ps1 | iex`
3. Poll `/api/poll?s=XXXXXXXX` toutes les 2 secondes
4. Quand les données arrivent → son pop (Web Audio API) + ouverture auto + analyse Claude
5. Rapport + chat IA avec historique persisté en localStorage

**LocalStorage keys** :
- `sospc_diagnostic_v1` — données système + rapport + historique chat
- `sospc_position_v1` — position du widget (drag)

**Comportements** :
- Drag via Pointer Events API + `setPointerCapture`
- Snap au bord le plus proche à la fermeture
- `ensurePanelVisible()` au resize/ouverture
- `body.cover-active` → `opacity:0; pointer-events:none` sur le widget
- Morph animation FAB → panel via transition CSS sur `#sospc-widget`

**URLs API** : `https://sos-pc-diagnostic.netlify.app/api/[analyze|chat|collect|poll]`

**Clé API** : `ANTHROPIC_API_KEY` dans les variables d'environnement Netlify du site `sos-pc-diagnostic`  
**Modèle** : `claude-haiku-4-5-20251001`

---

## Flow diagnostic complet

```
1. User ouvre le widget → voit la commande avec session ID
2. User ouvre PowerShell → colle la commande
3. diag.ps1 collecte : OS, CPU, RAM, GPU, disques, processus, startup, Event Log
4. diag.ps1 POST les données à /api/collect avec le session ID
5. /api/collect stocke dans Netlify Blobs (clé: session-XXXXXXXX)
6. Widget poll /api/poll → reçoit les données → son pop → ouverture auto
7. /api/poll supprime la clé après lecture (one-shot)
8. Widget appelle /api/analyze → Claude génère rapport JSON
9. Rapport affiché + chat disponible
10. Tout sauvegardé en localStorage (persist entre sessions)
```

---

## État actuel de la branche `design-alternative`

### Ce qui fonctionne ✓
- Design Tokyo Night complet (palette, fonts, grid background)
- Hero avec terminal mock animé
- Section services avec cards colorées par service
- Section diagnostic avec terminal mock + bouton chatbot
- Portfolio grid avec hover overlay
- Section contact avec liens Discord, Telegram, LinkedIn
- Google Maps embedé (Le Castellet 04700)
- Navbar avec hamburger mobile fonctionnel (DOMContentLoaded)
- Cover page (logique identique à `main`, inchangée)
- Widget chatbot v7 (morph FAB→panel, drag, son, localStorage)
- Scroll reveal déclenché après dismiss de la cover

### Ce qui reste à améliorer / problèmes connus
- **Délai après swipe** : le contenu apparaît avec un léger délai après la cover — partiellement résolu par le scroll reveal post-dismiss mais peut encore être amélioré
- **Encodage PowerShell** : `Get-Content` affiche les accents corrompus mais le fichier est correct en UTF-8 — ne pas se fier à l'affichage PowerShell, vérifier dans VSCode ou le preview Netlify
- **Clé API manquante** : `/api/analyze` et `/api/chat` retournent des erreurs si `ANTHROPIC_API_KEY` n'est pas configurée dans Netlify → à ajouter dans les env vars du site `sos-pc-diagnostic`
- **Page diagnostic standalone** (`/diagnostic`) : existe sur `main` mais pas intégrée dans `design-alternative`
- **AsciiBackground** : toujours avec l'ancien thème violet — à recolorer en teal pour la branche alternative
- **ThemeSwitcher** : le toggle light/dark fonctionne mais le thème light n'est pas optimisé pour Tokyo Night

### Fichiers modifiés vs `main`
| Fichier | Statut |
|---------|--------|
| `src/pages/index.astro` | Entièrement réécrit |
| `src/layouts/Layout.astro` | Variables CSS Tokyo Night + chatbot v7 |
| `src/components/Navbar.astro` | Entièrement réécrit (Tokyo Night) |
| `src/components/CoverPage.astro` | Identique à `main` (restauré) |

---

## Commandes utiles

```powershell
# Synchro locale
git fetch origin; git checkout design-alternative; git pull origin design-alternative

# Voir le diff avec main
git diff main..design-alternative --stat

# Revenir à un commit propre si problème
git log --oneline -10
git reset --hard <commit_hash>
git push --force origin design-alternative

# Preview local
npm run dev
```

---

## Points d'attention importants

1. **Ne jamais utiliser `git show branch:file > file`** — crée des null bytes qui cassent le build
2. **Ne jamais utiliser `Out-File` ou `Set-Content` sans `-Encoding UTF8`** avec des fichiers qui ont des accents — utiliser `[System.IO.File]::WriteAllText()` avec `UTF8Encoding::new($false)`
3. **Éviter les regex en chaîne sur des fichiers déjà modifiés** — préférer générer des fichiers complets
4. **`CoverPage.astro`** — ne pas modifier la logique JS, seulement le style CSS si nécessaire
5. **Les fonts Google** sont déjà dans le `<head>` du Layout — ne pas les rajouter

---

## Contact / infos SOS-PC
- Tel : 07 69 56 14 91
- Email : sos.pc.04@gmail.com
- Discord : https://discord.gg/APFtmK5PYc
- Telegram : https://t.me/+pWnKAEiqrJE4NzRk
- LinkedIn : https://www.linkedin.com/in/sos-pc-04700-castellet/
