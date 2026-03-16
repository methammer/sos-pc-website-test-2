# SOS-PC — Handoff Document
## Branche : `design-alternative`
## Repo : `github.com/methammer/sos-pc-website-test-2`
## Dernière mise à jour : 16 Mars 2026

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
  pages/contact-success.astro
  components/
    Navbar.astro              — Navbar Tokyo Night avec hamburger mobile
    CoverPage.astro           — Cover page Tokyo Night (swipe to reveal)
    AsciiBackground.astro     — Background ASCII anime (a revoir)
    ThemeSwitcher.astro       — Toggle dark/light
public/
  PC-repair/animations/      — Animation Lottie reparation PC
  Streaming/animations/      — Animation Lottie streaming
  web-design/animations/     — Animation Lottie web design
  diag.ps1 -> redirect vers sos-pc-diagnostic.netlify.app/diag.ps1
netlify.toml
```

### `sos-pc-diagnostic` — Les Netlify Functions
```
netlify/functions/
  analyze.js    — POST /api/analyze  — Gemini via Netlify AI Gateway
  chat.js       — POST /api/chat     — Chat IA avec contexte diagnostic
  collect.js    — POST /api/collect  — Stockage donnees via Netlify Blobs
  poll.js       — GET  /api/poll?s=  — Polling pour recuperer donnees
public/
  diag.ps1     — Script PowerShell collecte donnees PC (v2 etendu)
```

---

## Design — Tokyo Night Theme

### Palette CSS (`:root` dans `Layout.astro`)
```css
--bg:          #1a1b26
--bg-card:     #24283b
--bg-elevated: #2a2e42
--accent-hex:  #00d4aa
--text:        #c0caf5
--text-muted:  #7982a9
--border:      #3b4261
--yellow:      #e0af68
--red:         #f7768e
--blue:        #7aa2f7
--purple:      #bb9af7
```

### Fonts
- `JetBrains Mono` — elements tech, labels, monospace
- `Outfit` — corps de texte, titres

---

## Achievements session 15-16 Mars 2026

### 1. Section Contact — Redesign complet
- Grille 2 colonnes, boutons compacts avec logos SVG officiels
- Hover colore par app (Discord #5865F2, Telegram #229ED9, etc.)
- Ajout Telegram, LinkedIn, WhatsApp (placeholder)
- Banniere `pre-diagnostic.json` dans le form entre textarea et submit
- Champ `<input type="hidden" name="diagnostic-data">` pour Netlify Forms

### 2. Animations Lottie — Cartes services
- Chargement via CDN bodymovin (contourne le bug `define:vars` d'Astro)
- `autoplay: false` — play au `mouseenter`, pause au `mouseleave`
- Filtres CSS accordes aux couleurs des icones :
  - Teal   : `sepia(1) hue-rotate(130deg) saturate(3) brightness(0.9)`
  - Bleu   : `sepia(1) hue-rotate(195deg) saturate(4) brightness(1.1)`
  - Purple : `sepia(1) hue-rotate(240deg) saturate(3) brightness(1.0)`

### 3. Cover Page — Tokyo Night
- Fond `#1a1b26`, badge pulsant, titre degrade teal
- Ligne terminal decorative avec curseur clignotant
- Orbes supprimes
- Grid pattern desactive (`body::before { display: none }`)

### 4. Scroll reveal — Corrige
- Site visible sous la cover pendant le swipe (`visibility:hidden` supprime)
- Elements deja dans le viewport non masques (`getBoundingClientRect()` check)

### 5. Dark mode force par defaut
Script anti-FOUC dans `<head>` — force `dark` pour les nouveaux visiteurs,
respecte le choix explicite `light` via localStorage.

### 6. SEO — Priorites hautes
- `<meta name="description">` propre et geolocalise
- Open Graph complet (title, description, image, url, locale)
- Twitter Card
- Schema.org `LocalBusiness` (nom, tel, email, adresse 04700, zone)
- `<link rel="canonical" href="https://sos-pc.click">`

### 7. Google Maps
- Remplacement OpenStreetMap par Google Maps embed sans cle API

### 8. Portfolio — Images par le haut
- `object-position: top` sur `.pc-img-wrap img`

### 9. Migration IA — Netlify AI Gateway
- `analyze.js` + `chat.js` migres vers Gemini 2.0 Flash Lite
- Netlify AI Gateway injecte automatiquement `GEMINI_API_KEY` et `GOOGLE_GEMINI_BASE_URL`
- **NE PAS definir `GEMINI_API_KEY` manuellement dans les env vars Netlify**
- Header API : `x-goog-api-key` (REST Gemini v1beta)

### 10. Export diagnostic — Rapport texte lisible
`buildDiagPayload()` dans `Layout.astro` genere un rapport texte formate :
- Separateurs `===` et `---`
- Barres de progression disques `[####------]`
- Alerte antivirus INACTIF en haut du rapport si Defender desactive
- Sections : systeme, GPU, disques SMART, reseau, securite, temperatures,
  BSOD, problemes detectes, actions rapides, symptomes utilisateur, logiciels
- `filter(Boolean)` sur tous les tableaux (evite erreurs sur null)
- Injecte dans `<input name="diagnostic-data">` recu dans l'email Netlify

### 11. Reset diagnostic — Session propre
- `sospcReset()` pose `sessionStorage.setItem('sospc_reset', '1')`
- `DOMContentLoaded` verifie le flag avant de recharger les anciennes donnees
- Flag efface apres generation du nouveau sessionId
- "Nouveau scan" repart de zero meme apres rechargement de page

### 12. Notifications email Netlify Forms
- Email : `sos.pc.04@gmail.com`
- Reply-To = email du visiteur
- Sujet dynamique via champ `name="subject"`

### 13. Script `diag.ps1` — Version 2
Nouvelles donnees collectees vs v1 :
- Pagefile utilisation, plusieurs GPU avec date driver/resolution/refresh
- SMART disques : type SSD/NVMe, sante, heures, temperature, secteurs defaillants
- Reseau : adaptateurs actifs, IP, test internet (`8.8.8.8`), latence DNS
- Securite : Defender (enabled, realtime, date signatures), pare-feu (3 profils), UAC
- Mises a jour (5 derniers hotfixes)
- Temperatures ACPI (sans install tierce)
- BSOD 7 derniers jours (event ID 41/1001)
- Performance : disque I/O %, RAM %, pagefile %
- Liste complete logiciels tiers

Filtres logiciels :
- Exclus : Microsoft Corporation, Windows SDK, composants .NET/Runtime
- Exclus : composants Python multi-entrees, KB hotfixes, redistributables
- Exclus : composants Visual Studio internes (vs_*, vcpp_*)
- Resultat : ~30-50 logiciels tiers au lieu de 200+

---

## Ce qui reste a faire

- **AsciiBackground** : revoir concept (demo particules Tokyo Night faite en chat, pas encore deploye)
- **ThemeSwitcher** : theme light non optimise pour Tokyo Night
- **Page `/diagnostic` standalone** : existe sur `main`, pas integree dans `design-alternative`
- **WhatsApp** : bouton placeholder a activer quand numero disponible
- **Heures SMART NVMe** : retourne `?` sur certains disques — limite de WMI sur NVMe

---

## Fichiers modifies vs `main`

| Fichier | Statut |
|---------|--------|
| `src/pages/index.astro` | Entierement reecrit + export diagnostic + Lottie |
| `src/layouts/Layout.astro` | Tokyo Night + chatbot v8 + rapport texte + reset propre |
| `src/components/Navbar.astro` | Tokyo Night |
| `src/components/CoverPage.astro` | Tokyo Night (orbes supprimes) |
| `netlify/functions/analyze.js` (diagnostic) | Gemini + Netlify AI Gateway |
| `netlify/functions/chat.js` (diagnostic) | Gemini + mapping historique corrige |
| `public/diag.ps1` (diagnostic) | v2 etendu, filtres logiciels |

---

## Points d'attention importants

1. **Encodage** : toujours `[System.IO.File]::WriteAllText()` avec `UTF8Encoding::new($false)`
2. **BOM UTF-8** : si build echoue avec "Unknown character 65279" — réécrire `netlify.toml` sans BOM
3. **Netlify AI Gateway** : ne jamais definir `GEMINI_API_KEY` manuellement dans les env vars de `sos-pc-diagnostic`
4. **Template literals esbuild** : dans les Netlify Functions, eviter les backticks imbriques dans les `map()` — utiliser tableau `lines[]` + concatenation simple
5. **Gemini format historique** : `role: "user" | "model"` (pas "assistant"), premier message doit etre "user"
6. **Replace PowerShell multilignes** : souvent echoue (CRLF) — preferer la methode par index de ligne avec `ReadAllLines`
7. **`filter(Boolean)`** : les tableaux dans `diagData` peuvent contenir des `null` (ex: `network.adapters`)
8. **Modifications ciblees** : toujours recuperer le fichier actuel avant de modifier

---

## Commandes utiles

```powershell
# Synchro locale
git fetch origin
git pull origin design-alternative

# Annuler modifications locales non commitees
git checkout -- src/pages/index.astro

# Revenir a un commit
git log --oneline -10
git reset --hard <hash>
git push --force origin design-alternative

# Lister modeles Gemini disponibles sur une cle
$key = "TA_CLE"
$r = Invoke-RestMethod "https://generativelanguage.googleapis.com/v1beta/models?key=$key"
$r.models | Where-Object { $_.supportedGenerationMethods -contains "generateContent" } | Select-Object name, displayName

# Modifier un fichier par index de ligne (methode fiable)
$f = "src\layouts\Layout.astro"
$lines = [System.IO.File]::ReadAllLines((Resolve-Path $f), [System.Text.UTF8Encoding]::new($false))
$lines = $lines[0..N] + "nouvelle ligne" + $lines[(N+1)..($lines.Count-1)]
[System.IO.File]::WriteAllLines((Resolve-Path $f), $lines, [System.Text.UTF8Encoding]::new($false))
```

---

## Contact SOS-PC
- Tel : 07 69 56 14 91
- Email : sos.pc.04@gmail.com
- Discord : https://discord.gg/APFtmK5PYc
- Telegram : https://t.me/+pWnKAEiqrJE4NzRk
- LinkedIn : https://www.linkedin.com/in/sos-pc-04700-castellet/
