# SOS-PC — Handoff Document
## Branche : `design-alternative`
## Repo : `github.com/methammer/sos-pc-website-test-2`
## Dernière mise à jour : 15 Mars 2026

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
  layouts/Layout.astro       — Layout global + chatbot widget flottant v8
  pages/index.astro          — Page principale (design-alternative)
  pages/diagnostic.astro     — Page diagnostic standalone (branche main)
  pages/contact-success.astro
  components/
    Navbar.astro              — Navbar Tokyo Night avec hamburger mobile
    CoverPage.astro           — Cover page Tokyo Night (swipe to reveal)
    AsciiBackground.astro     — Background ASCII animé
    ThemeSwitcher.astro       — Toggle dark/light
    LottieAnimation.astro     — Wrapper animations Lottie (non utilisé directement)
public/
  PC-repair/animations/      — Animation Lottie réparation PC
  Streaming/animations/      — Animation Lottie streaming
  web-design/animations/     — Animation Lottie web design
  diag.ps1 → redirect vers sos-pc-diagnostic.netlify.app/diag.ps1
netlify.toml                  — Redirects dont /diag.ps1
```

### `sos-pc-diagnostic` — Les Netlify Functions
```
netlify/functions/
  analyze.js    — POST /api/analyze  — Appel Gemini via Netlify AI Gateway
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

---

## Achievements de la session (15 Mars 2026)

### 1. Section Contact — Redesign complet
**Fichier** : `src/pages/index.astro`

- Grille 2 colonnes pour les boutons de contact
- Logos SVG officiels de chaque app avec couleurs de marque au hover
- Boutons compacts avec label + valeur
- Ajout de **Telegram** (`t.me/+pWnKAEiqrJE4NzRk`), **LinkedIn** et **WhatsApp** (placeholder désactivé)
- Couleurs hover par app : Téléphone `#25D366`, Email `#EA4335`, Discord `#5865F2`, Telegram `#229ED9`, LinkedIn `#0A66C2`, WhatsApp `#25D366`

### 2. Animations Lottie dans les cartes services
**Fichier** : `src/pages/index.astro`

- Animations JSON chargées depuis `/public/PC-repair/`, `/public/Streaming/`, `/public/web-design/`
- Chargement via CDN bodymovin (pas de composant Astro — bug `define:vars` évité)
- Positionnées en `absolute` bas-droite des cartes, `opacity: 0.18` au repos
- Filtre CSS pour teinte par carte (teal/bleu/purple)
- `autoplay: false` — animation déclenchée uniquement au `mouseenter`, pausée au `mouseleave`
- Au hover : `opacity: 0.5` + déplacement subtil

### 3. Cover Page — Thème Tokyo Night
**Fichier** : `src/components/CoverPage.astro`

- Fond `#1a1b26` (Tokyo Night) remplace le dégradé violet
- Grid pattern teal en background (cohérent avec le reste du site)
- Deux glow orbs animés (teal + bleu)
- Badge avec point pulsant vert
- Titre en dégradé `c0caf5 → teal`
- Ligne terminal décorative avec curseur clignotant
- Bouton scroll `// scroll to explore` en monospace
- Logique JS de dismiss identique à l'original

### 4. Scroll reveal — Approche corrigée
**Fichier** : `src/pages/index.astro`

- Le site est rendu immédiatement sous la cover (visible pendant le swipe)
- `initReveal()` lancé dès le chargement sans attendre le dismiss
- Les éléments **déjà dans le viewport** ne sont pas masqués (`getBoundingClientRect()` check)
- Seuls les éléments hors viewport reçoivent l'animation d'entrée au scroll

### 5. Diagnostic — Export vers formulaire de contact
**Fichiers** : `src/layouts/Layout.astro` + `src/pages/index.astro`

**Widget (Layout.astro)** :
- Bouton `Joindre au formulaire de contact` dans le panel rapport
- `buildDiagPayload()` compile : score, système, problèmes, symptômes utilisateur du chat, historique complet
- Auto-attach dès que l'analyse est terminée (sans action utilisateur)
- Mise à jour du payload après chaque message chat (nouveaux symptômes inclus)
- Event `sospc:attach-diag` dispatché vers index.astro
- Event `sospc:diag-reset` pour retirer la pièce jointe au reset

**Formulaire (index.astro)** :
- `<input type="hidden" name="diagnostic-data">` dans le form
- Bannière `pré-diagnostic.json` positionnée **dans le form** entre le textarea et le bouton submit
- Affiche : date, score, nb problèmes, nb symptômes décrits
- Bouton `×` pour détacher le diagnostic
- Bouton submit change de libellé : "Envoyer + diagnostic joint"
- Auto-attach au `DOMContentLoaded` si un diagnostic existe en localStorage
- Pré-remplit le sujet : "Suite diagnostic SOS-PC du JJ/MM/AAAA"

### 6. Google Maps
**Fichier** : `src/pages/index.astro`

- Remplacement d'OpenStreetMap par Google Maps embed (sans clé API)
- URL : `https://maps.google.com/maps?q=Le+Castellet,+04700,+France&output=embed`

### 7. Migration API IA vers Gemini + Netlify AI Gateway
**Fichiers** : `netlify/functions/analyze.js` + `netlify/functions/chat.js`

- Migration de Claude Haiku (Anthropic) vers **Gemini 2.0 Flash Lite**
- Utilisation du **Netlify AI Gateway** — plus besoin de gérer de clé API manuellement
- Netlify injecte automatiquement `GEMINI_API_KEY` et `GOOGLE_GEMINI_BASE_URL`
- Header API : `x-goog-api-key` (REST Gemini v1beta)
- `analyze.js` : prompt construit avec tableau `lines[]` (évite les template literals dans esbuild)
- `chat.js` : gestion de l'historique Gemini (`role: "user" | "model"`) avec injection du contexte système dans le premier message user, et gestion des historiques commençant par `model`
- CORS : `Access-Control-Allow-Origin: *` sur toutes les fonctions

### 8. Notifications email formulaire
- Configuration Netlify : **Project configuration → Notifications → Form submission notifications**
- Email de destination : `sos.pc.04@gmail.com`
- `Reply-To` automatique = email du visiteur (champ `name="email"` dans le form)
- Sujet dynamique défini par le JS (via champ `name="subject"`)

---

## Chatbot widget flottant — État actuel

**Fichier** : inline dans `src/layouts/Layout.astro`  

**Flow** :
1. Génère un session ID aléatoire (`$s='XXXXXXXX'`)
2. Affiche la commande : `$s='XXXXXXXX'; irm https://sos-pc.click/diag.ps1 | iex`
3. Poll `/api/poll?s=XXXXXXXX` toutes les 2 secondes
4. Quand les données arrivent → son pop (Web Audio API) + ouverture auto + analyse Gemini
5. Rapport + chat IA + bouton "Joindre au formulaire"
6. Tout sauvegardé en localStorage

**LocalStorage keys** :
- `sospc_diagnostic_v1` — données système + rapport + historique chat
- `sospc_position_v1` — position du widget (drag)

**URLs API** : `https://sos-pc-diagnostic.netlify.app/api/[analyze|chat|collect|poll]`  
**Modèle IA** : `gemini-2.0-flash-lite` via Netlify AI Gateway  
**Clé API** : injectée automatiquement par Netlify (ne pas définir `GEMINI_API_KEY` manuellement)

---

## Flow diagnostic complet (mis à jour)

```
1. User ouvre le widget → voit la commande avec session ID
2. User ouvre PowerShell → colle la commande
3. diag.ps1 collecte : OS, CPU, RAM, GPU, disques, processus, startup, Event Log
4. diag.ps1 POST les données à /api/collect avec le session ID
5. /api/collect stocke dans Netlify Blobs (clé: session-XXXXXXXX)
6. Widget poll /api/poll → reçoit les données → son pop → ouverture auto
7. /api/poll supprime la clé après lecture (one-shot)
8. Widget appelle /api/analyze → Gemini génère rapport JSON
9. Rapport affiché + chat disponible + bouton "Joindre au formulaire"
10. Payload JSON auto-attaché au formulaire de contact (#contact)
11. User remplit le form → submit → email reçu sur sos.pc.04@gmail.com
12. Tout sauvegardé en localStorage (persist entre sessions)
```

---

## État actuel — Ce qui fonctionne ✓

- Design Tokyo Night complet (palette, fonts, grid background)
- Cover page Tokyo Night avec animations
- Hero avec terminal mock animé
- Section services avec cards + animations Lottie au hover
- Section diagnostic avec terminal mock + bouton chatbot
- Portfolio grid avec hover overlay
- Section contact : grille 2 colonnes, 6 boutons avec logos SVG + hover coloré
- Google Maps embedé (Le Castellet 04700)
- Navbar avec hamburger mobile fonctionnel
- Widget chatbot (morph FAB→panel, drag, son, localStorage)
- Diagnostic IA fonctionnel via Gemini 2.0 Flash Lite + Netlify AI Gateway
- Chat IA avec historique persisté
- Export diagnostic → formulaire de contact (bannière pré-diagnostic.json)
- Formulaire Netlify avec notification email vers sos.pc.04@gmail.com
- Scroll reveal correct (éléments viewport non masqués)
- Site pré-rendu visible pendant le swipe de la cover

---

## Ce qui reste à faire / problèmes connus

- **AsciiBackground** : toujours avec l'ancien thème violet — à recolorer en teal
- **ThemeSwitcher** : thème light non optimisé pour Tokyo Night
- **Page diagnostic standalone** (`/diagnostic`) : existe sur `main` mais pas intégrée dans `design-alternative`
- **WhatsApp** : bouton placeholder désactivé — à activer quand le numéro est disponible

---

## Fichiers modifiés vs `main`

| Fichier | Statut |
|---------|--------|
| `src/pages/index.astro` | Entièrement réécrit + export diagnostic |
| `src/layouts/Layout.astro` | Tokyo Night + chatbot v8 + export diagnostic |
| `src/components/Navbar.astro` | Entièrement réécrit (Tokyo Night) |
| `src/components/CoverPage.astro` | Entièrement réécrit (Tokyo Night) |
| `netlify/functions/analyze.js` (repo diagnostic) | Migré vers Gemini + Netlify AI Gateway |
| `netlify/functions/chat.js` (repo diagnostic) | Migré vers Gemini + Netlify AI Gateway |

---

## Points d'attention importants

1. **Ne jamais utiliser `git show branch:file > file`** — crée des null bytes qui cassent le build
2. **Toujours utiliser `[System.IO.File]::WriteAllText()` avec `UTF8Encoding::new($false)`** pour écrire des fichiers avec accents sous PowerShell
3. **Ne jamais utiliser `Set-Content` ou `Out-File` sans vérification** — risque de BOM UTF-8 qui casse le `netlify.toml`
4. **`netlify.toml`** — si corrompu (BOM), le build échoue avec "Unknown character 65279". Fix : lire le fichier avec Python et réécrire sans BOM
5. **Netlify AI Gateway** : ne pas définir `GEMINI_API_KEY` manuellement dans les env vars de `sos-pc-diagnostic` — sinon Netlify ne surcharge pas avec sa propre clé gateway
6. **Template literals dans esbuild (Netlify Functions)** : éviter les backticks imbriqués dans les `map()` — utiliser des tableaux `lines[]` + concaténation simple
7. **Gemini API** : format historique `role: "user" | "model"` (pas "assistant"), premier message doit être "user"
8. **Modifications ciblées** : toujours demander le fichier actuel avant de modifier — ne pas partir du fichier local qui peut diverger du repo

---

## Commandes utiles

```powershell
# Synchro locale depuis GitHub
git fetch origin
git pull origin design-alternative

# Voir le diff avec main
git diff main..design-alternative --stat

# Revenir à un commit propre si problème
git log --oneline -10
git reset --hard <commit_hash>
git push --force origin design-alternative

# Preview local
npm run dev

# Vérifier le modèle Gemini disponible sur une clé
$key = "TA_CLE"
$r = Invoke-RestMethod "https://generativelanguage.googleapis.com/v1beta/models?key=$key"
$r.models | Where-Object { $_.supportedGenerationMethods -contains "generateContent" } | Select-Object name, displayName
```

---

## Contact / infos SOS-PC
- Tel : 07 69 56 14 91
- Email : sos.pc.04@gmail.com
- Discord : https://discord.gg/APFtmK5PYc
- Telegram : https://t.me/+pWnKAEiqrJE4NzRk
- LinkedIn : https://www.linkedin.com/in/sos-pc-04700-castellet/