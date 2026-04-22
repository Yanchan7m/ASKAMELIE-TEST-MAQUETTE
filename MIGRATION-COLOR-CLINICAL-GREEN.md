# Migration Clinical Green — palette vert flash / vert clair / vert simple

Document dédié au passage du branding violet vers la **palette vert "Clinical Green"** du 22 avril 2026 soir, **plus tous les ajustements qui en ont découlé** (couronnes, popups, timings).

- **Commit racine** : `8b18848` (palette swap violet → vert)
- **Commits suivants** : 14 ajustements (couronnes, centrage, réductions, scroll-triggered)
- **Fichiers touchés** : `index.html`, `ecn-focus.html`

---

## 🎨 1. Palette "Clinical Green" (fondation)

| Rôle | Couleur | Hex | HSL | Usage |
|---|---|---|---|---|
| **Vert principal** (simple) | Teal profond | `#0E5552` + `#2E8B7B` | 171°, 50%, 36% | Nav, titres, borders, icônes principales, branding |
| **Vert clair** | Menthe pâle | `#E8F4F1` / `#EAF0EA` | 168°, 31%, 93% | Backgrounds pills, cercles d'icônes, sections alternées |
| **Vert flash** | Vert vif | `#00E676` (light) / `#00B85E` (dark) | 145°, 100%, 45% | **CTA uniquement** (bouton principal), checkmarks, feedback réussite, highlights, couronnes |

> **Règle** : max 1 élément vert flash actif par écran. Le vert flash ne doit jamais être décoratif — il est réservé aux CTA et aux éléments actionables.

---

## 🔄 2. Table de correspondance (violet → vert)

Remplacement global effectué via `perl -pi -e`. Toutes les occurrences :

| Ancien (violet) | Nouveau (vert) | Contexte |
|---|---|---|
| `#A855F7` | `#00E676` | Main violet → vert flash |
| `#7c3aed` | `#00B85E` | Violet foncé → vert flash foncé (gradient end, hover) |
| `#6B2FD6` | `#00A050` | Violet hover state → vert flash hover |
| `rgba(168,85,247,X)` | `rgba(0,230,118,X)` | Shadows et highlights |
| `rgba(124,58,237,X)` | `rgba(0,184,94,X)` | Gradient variantes |
| `#F5EEFE` | `#E8F4F1` | Background lavande → menthe pâle |
| `#EDE4FA` | `#C8E8D8` | Bordure lavande → menthe |
| `#FBF7FE` | `#F0F9F4` | Hover background lavande → menthe |
| `#D9C3F7` | `#9FDCBF` | Bordure tag premium lavande → menthe |

### Commande perl reproductible pour la prod

```bash
perl -pi -e "
  s/#A855F7/#00E676/g;
  s/#7c3aed/#00B85E/g;
  s/#6B2FD6/#00A050/g;
  s/rgba\(168,85,247,/rgba(0,230,118,/g;
  s/rgba\(124,58,237,/rgba(0,184,94,/g;
  s/#F5EEFE/#E8F4F1/g;
  s/#EDE4FA/#C8E8D8/g;
  s/#FBF7FE/#F0F9F4/g;
  s/#D9C3F7/#9FDCBF/g;
" path/to/your/files.{html,css,scss,js,jsx,tsx,vue}
```

Ne pas oublier les SVG data URI : `fill%3D%22%237c3aed%22` → `fill%3D%22%2300B85E%22` (car `%23` = `#`).

---

## 👑 3. Couronne unifiée partout

### Décision finale
**SVG couronne simple 3-peaks + base bar** (path : `M3 17h18l-1.5-9L15 12 12 5 9 12 4.5 8 3 17zm0 2h18v2H3v-2z`), viewBox 24×24, fill `#00B85E` (vert flash foncé).

### Historique des itérations (à ne pas reproduire en prod)
1. Première tentative : emoji 👑 — trop informel, multicolore
2. Deuxième tentative : symbole Unicode ♛ (Chess Queen) — rendu variable selon fonts
3. Troisième tentative : FontAwesome crown (viewBox 640×512, détails 5-peaks/jewels) — trop complexe
4. **Finale** : simple SVG 3-peaks + base bar, vert flash `#00B85E`

### Emplacements unifiés de la couronne

| Emplacement | Sélecteur | Taille |
|---|---|---|
| Popup "Demander à Amélie" | `.ask-gate-crown svg` | 32×32 dans cercle 64×64 (desktop) ; 26×26 dans 52×52 (mobile) |
| Popup paywall ecn-focus | `.pw-lock svg` | 28×28 dans cercle 56×56 |
| Popup paywall index.html | `.pw-lock-wrap svg` | 28×28 dans cercle 56×56 |
| Card CTA fin de quiz | `.report-unlock-crown svg` | 28×28 dans cercle 56×56 |
| Badge "ILLIMITÉ" year card | `.year-card.locked::after` data URI | 10×10 |
| Badge year switcher | `.ys-card.locked::after` data URI | 9×9 |
| Badge dossier verrouillé | `.dossier-card .lock-badge::before` data URI | 11×11 |
| Icône "voir plus" | `.q-voir-plus::before` data URI | 14×14 |
| Icône dossier locked inline | JS inline `<svg>` (lockBadge var) | 16×16 |

### Cercle d'accueil (icon wrap)
Toutes les couronnes principales sont dans un cercle :
```css
width:56px;height:56px;border-radius:50%;
background:#EAF0EA;border:1px solid #C4D3C5;
```
(variantes de taille selon contexte)

### Cadenas 🔒 supprimé partout sauf "Paiement sécurisé"
Le cadenas est réservé au contexte security/Stripe ("🔒 Paiement sécurisé · ton email reste 100% privé"). Partout ailleurs, couronne.

---

## 🪟 4. Popups : uniformisation design + comportement

### Centrage sur tous les devices
**Tous les popups** (`.paywall`, `.ask-gate`, `#firstQGate`) : `align-items:center` + `justify-content:center` même sur mobile. Fin du "bottom sheet" sur ask-gate mobile.

### Popup "Demander à Amélie" — refonte complète

**Structure finale** :
```
┌──────────────────────────────────────┐
│                            [ × ]     │  ← close
│              [👑]                     │  ← couronne verte dans cercle vert
│  ⟪ RÉSERVÉ AU FOCUS ILLIMITÉ ⟫        │  ← tag violet … wait, vert menthe
│  Amélie IA, ton assistante médicale  │
│  Pose-lui n'importe quelle question…│  ← 1 seul paragraphe (réduit de 2)
│  ┌────────────────────────┐          │
│  │  Chat preview vert      │          │
│  └────────────────────────┘          │
│  [  Passer au plan Focus Illimité → ]│  ← bouton vert flash
│               Plus tard              │
└──────────────────────────────────────┘
```

**Dimensions finales** :
- `max-width: 420px` (au lieu de 460px)
- `padding: 28px 32px 22px` (32px horizontal après plusieurs itérations 20→24→32)
- Tag "Réservé au Focus Illimité" : background `#E8F4F1`, border `#9FDCBF`, color `#00B85E`

**Texte réduit** (effet Hick) :
- Avant : 2 paragraphes (~380 caractères)
- Après : 1 paragraphe (~140 caractères) : "Pose-lui n'importe quelle question pendant ton quiz. Elle repère tes faiblesses et adapte les prochains dossiers."
- Justification : le chat preview illustre déjà l'usage, pas besoin de répéter

### Popup paywall (simplification titre)

**Avant** : "🔒 Cette année est réservée" avec long sous-titre + 4 features + CTA
**Après** : "ECN 2018" / "EDN 2024" (dynamique) + "Accessible uniquement via **Focus Illimité**." + 4 features + CTA

Titre **plus court et plus direct**. L'emoji 🔒 est retiré (la couronne SVG dans le cercle au-dessus suffit).

### Popup titres dynamiques (`openPaywall(context)`)

| Context | Titre | Sub |
|---|---|---|
| `year-2017` à `year-2022` | `ECN <année>` | Accessible uniquement via Focus Illimité |
| `year-2023` à `year-2025` | `EDN <année>` | Idem |
| `bilan-locked` | Ton bilan personnalisé | Pitch bilan détaillé |
| `explanation-locked` | Correction complète réservée | Pitch corrigés Amélie |
| `exam-locked`/`qi-locked` | Cette épreuve est réservée | Pitch DCP/QI/LCA |
| `post-signup` | 🎉 Bienvenue chez Ask Amélie | Upsell post-signup |
| default | Accès Focus Illimité | Pitch général |

---

## 🎓 5. Fin de quiz — ajustements

### Card CTA "Ton bilan personnalisé t'attend"

**Ajouts** :
- Couronne SVG verte dans cercle vert pâle **en haut** de la card (`.report-unlock-crown`)
- Même style visuel que les popups → cohérence

**CTA modifié** :
- ~~`openPaywall('bilan-locked')`~~ → **`showFirstQuestionGate(false)`**
- Le bouton "Passer à Focus Illimité →" ouvre maintenant directement le **gate de création de compte**, pas la popup paywall

### Bloc doublon supprimé

Avant : sous la card `.report-unlock`, il y avait aussi :
> Crée ton compte pour continuer les autres dossiers.
> [Créer mon compte →]

**Supprimé entièrement** : un seul CTA suffit pour éviter le double-messaging.

### Score card tailles réduites ~20%

| Élément | Avant | Après |
|---|---|---|
| Padding card | 40px 32px | **30px 26px** |
| Headline "Tu progresses." | clamp(22, 4vw, 30px) | **clamp(18, 3vw, 24px)** |
| Big num "40%" | clamp(70, 14vw, 100px) | **clamp(56, 11vw, 80px)** |
| Meta "4/10 · 12:00" font-size | 14/20/13 | **13/17/12** |
| Meta margin | 20px 0 32px | **14px 0 20px** |
| Header margin-bottom | 22px | **16px** |

Card moins imposante, laisse plus de place pour le bilan.

### Animation "Amélie réfléchit" → scroll-triggered

**Problème** : l'animation démarrait dans `renderScore()` et durait 3s. Si l'utilisateur mettait >3s à scroller jusqu'au bloc, il voyait déjà le bilan final sans jamais voir l'animation.

**Fix** : `IntersectionObserver` sur `#amelieThinking` avec `threshold: 0.25`. L'animation + l'API call démarrent **quand le bloc entre à 25% dans le viewport**. Le `MIN_THINK_MS` de 3000ms se compte à partir de ce moment.

**Fallback** : navigateurs sans IntersectionObserver → démarre directement.

```js
var _thinkingEl = document.getElementById('amelieThinking');
if (_thinkingEl && 'IntersectionObserver' in window) {
  var _obs = new IntersectionObserver(function(entries) {
    entries.forEach(function(entry) {
      if (entry.isIntersecting) {
        _obs.disconnect();
        startThinking();
        _runBilanApi();
      }
    });
  }, { threshold: 0.25 });
  _obs.observe(_thinkingEl);
}
```

---

## 📋 6. Récap modifications visuelles par zone

### 🟢 Éléments passés au VERT FLASH (ex-violet)

**CTA principaux** (bouton unique par popup) :
- "Passer à Focus Illimité →" (paywall popups)
- "Passer au plan Focus Illimité" (ask-gate)
- "Passer à Focus Illimité →" (card fin de quiz, maintenant → signup)
- "Demander à Amélie" (inline dans feedback quiz)

**Checkmarks ✓** :
- `.pw-feats li::before` (popups paywall)
- `.report-unlock-feats li::before` (card fin de quiz)
- Gradient : `linear-gradient(135deg, #00E676 0%, #00B85E 100%)`

**Badges "ILLIMITÉ"** :
- Year cards / year switcher / dossier lock badges
- Gradient `#00E676 → #00B85E` + crown SVG blanc + texte "ILLIMITÉ"

**Accents texte** :
- Headline score ("Tu progresses.") : `color: #00B85E`
- Surligneur "+300 places" : `background: rgba(0,230,118,.3)`
- Contour pointillé stakes breakdown : `2px dashed #00E676`

**Couronne** dans tous les cercles d'icônes des popups : `color: #00B85E`

### 🟢 Éléments en VERT CLAIR (menthe pâle)

**Pills backgrounds** :
- `.pw-feats li` : `background: #EAF0EA; border: 1px solid #C4D3C5`
- `.report-unlock-feats li` : idem
- `.ask-gate-tag` : `background: #E8F4F1; border: 1px solid #9FDCBF`

**Cercles d'icônes** (crown wraps) :
- `.ask-gate-crown`, `.pw-lock`, `.pw-lock-wrap`, `.report-unlock-crown`, `#firstQGate .fqg-icon`
- `background: #EAF0EA; border: 1px solid #C4D3C5`

### 🟢 Éléments en VERT SIMPLE (teal)

- Branding général (nav, titres, accents titre)
- Couleur `.report-unlock-feats li strong` : `#0E5552`
- Accent titre "ton assistante médicale" : gradient `#0E5552 → #1B6E68`
- Border shadows : `rgba(14,85,82,X)`

---

## 🚨 7. Vérifications à faire après migration prod

1. **Contraste AA** : vérifier que `#00E676` sur fond blanc respecte AA pour le texte (ratio ≥ 4.5:1). Pour les boutons avec texte blanc, préférer `#00B85E` en background ou ajouter une overlay sombre.
2. **Cohérence** : s'assurer qu'aucun élément décoratif n'utilise le vert flash (réservé action uniquement).
3. **SVG crown data URIs** : vérifier que les `fill="%2300B85E"` (encoded pour `#00B85E`) sont correctement percent-encodés dans tous les fichiers.
4. **Couronne centrée dans les cercles** : sur mobile, la couronne doit être SVG à ~50% du wrap (ex: 26×26 dans 52×52), pas remplir tout le cercle.
5. **IntersectionObserver** : vérifier le fallback pour vieux navigateurs (démarrer l'animation directement).
6. **Popups centrés** : `align-items:center` partout, pas de `flex-end` résiduel.

---

## 📦 8. Historique chronologique des 14 commits

| # | Hash | Heure | Titre |
|---|---|---|---|
| 1 | `8b18848` | 20:14 | Palette Clinical Green (violet → vert flash) — **fondation** |
| 2 | `6ebabf7` | 21:09 | Doc migration Clinical Green |
| 3 | `530c5c1` | 21:12 | Popups : cadenas → couronne + fix mobile |
| 4 | `2061447` | 21:16 | Popup ask-Amélie centrée mobile |
| 5 | `fe0d53d` | 21:19 | Popup ask-Amélie texte réduit (Hick) |
| 6 | `2c3174f` | 21:21 | Popup ask-Amélie width/padding |
| 7 | `382541c` | 21:22 | Padding +4px |
| 8 | `9702818` | 21:24 | Padding 32px (final) |
| 9 | `eefb9ed` | 21:29 | Couronne FA classique (tentative abandonnée) |
| 10 | `9743a4b` | 21:35 | Couronne uniforme simple 3-peaks (finale) |
| 11 | `f4e040e` | 21:43 | Couronne verte `#00B85E` |
| 12 | `590dc96` | 21:46 | Fin quiz couronne verte + card CTA |
| 13 | `d9b3e80` | 21:51 | CTA fin quiz → signup gate + suppression doublon |
| 14 | `d6563db` | 21:58 | Animation scroll-into-view |
| 15 | `176e7f2` | 22:00 | Score card réduite 20% |

---

## 📄 Fichiers associés

- `CHANGELOG-2026-04-22.md` : historique complet de la journée (43+ commits)
- `MIGRATION-PROD.md` : guide global avec visuels ASCII + matrice des popups
- **Ce document** : focus sur le passage Clinical Green et ses ajustements

Repo GitHub : `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`.
