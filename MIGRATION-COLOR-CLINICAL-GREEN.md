# Migration Clinical Green — palette vert flash / vert clair / vert simple

Document dédié au passage du branding violet vers la **palette vert "Clinical Green"** du 22 avril 2026 soir, **plus tous les ajustements qui en ont découlé** (couronnes, popups, timings, alignements).

- **Commit racine** : `8b18848` (palette swap violet → vert)
- **17 commits post-swap** jusqu'à `10a090e` (alignement blocks fin de quiz)
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
**SVG couronne simple 3-peaks + base bar**, viewBox 24×24, fill `#00B85E`.

Path : `M3 17h18l-1.5-9L15 12 12 5 9 12 4.5 8 3 17zm0 2h18v2H3v-2z`

### Historique des itérations (à ne pas reproduire en prod)
1. Emoji 👑 — trop informel, multicolore, rend différemment par OS
2. Symbole Unicode ♛ (Chess Queen) — rendu variable selon fonts
3. FontAwesome crown (viewBox 640×512, 5-peaks + jewels) — trop complexe
4. **Finale** : simple SVG 3-peaks + base bar, vert flash `#00B85E`

### Emplacements unifiés (9 endroits)

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
| Dossier locked inline | JS `lockBadge` inline `<svg>` | 16×16 |

### Cercle d'accueil (icon wrap)
Toutes les couronnes principales sont dans un cercle :
```css
width:56px;height:56px;border-radius:50%;
background:#EAF0EA;border:1px solid #C4D3C5;
```
(variantes de taille selon contexte)

### Cadenas 🔒 supprimé partout sauf "Paiement sécurisé"
Le cadenas est réservé au contexte security/Stripe ("🔒 Paiement sécurisé · ton email reste 100% privé"). Partout ailleurs, **couronne**.

---

## 🪟 4. Popups : uniformisation design + comportement

### Centrage sur tous les devices
**Tous les popups** (`.paywall`, `.ask-gate`, `#firstQGate`) : `align-items:center` + `justify-content:center` **même sur mobile**. Fin du "bottom sheet" sur ask-gate mobile.

### Popup "Demander à Amélie" — refonte complète

**Dimensions finales** :
- `max-width: 420px` (au lieu de 460px)
- `padding: 28px 32px 22px` (32px horizontal après itérations 20→24→32)
- Tag "Réservé au Focus Illimité" : background `#E8F4F1`, border `#9FDCBF`, color `#00B85E`

**Texte réduit** (effet Hick — moins de choix = décision plus rapide) :
- Avant : 2 paragraphes (~380 caractères)
- Après : 1 paragraphe (~140 caractères)
  > "Pose-lui n'importe quelle question pendant ton quiz. Elle repère tes faiblesses et adapte les prochains dossiers."

### Popup paywall (simplification titre)

**Avant** : "🔒 Cette année est réservée" avec long sous-titre
**Après** : "ECN 2018" / "EDN 2024" (dynamique) + "Accessible uniquement via **Focus Illimité**."

Titres plus courts + emoji 🔒 retiré (la couronne SVG dans le cercle suffit).

### Titres dynamiques (`openPaywall(context)`)

| Context | Titre | Sub |
|---|---|---|
| `year-2017`…`year-2022` | `ECN <année>` | Accessible uniquement via Focus Illimité |
| `year-2023`…`year-2025` | `EDN <année>` | Idem |
| `bilan-locked` | Ton bilan personnalisé | Pitch bilan détaillé |
| `explanation-locked` | Correction complète réservée | Pitch corrigés Amélie |
| `exam-locked`/`qi-locked` | Cette épreuve est réservée | Pitch DCP/QI/LCA |
| `post-signup` | 🎉 Bienvenue chez Ask Amélie | Upsell post-signup |
| default | Accès Focus Illimité | Pitch général |

---

## 🎓 5. Fin de quiz — ajustements majeurs

### Card CTA "Ton bilan personnalisé t'attend"

**Ajouts** :
- Couronne SVG verte dans cercle vert pâle **en haut** de la card (`.report-unlock-crown`)
- Même style visuel que les popups → cohérence

**CTA modifié** :
- ~~`openPaywall('bilan-locked')`~~ → **`showFirstQuestionGate(false)`**
- Le bouton "Passer à Focus Illimité →" ouvre maintenant directement le **gate de création de compte**, pas la popup paywall

### Bloc doublon supprimé

**Supprimé** : sous la card `.report-unlock`, il y avait aussi "Crée ton compte pour continuer les autres dossiers. [Créer mon compte →]" — **supprimé entièrement**, un seul CTA suffit.

### Score card tailles réduites ~20%

| Élément | Avant | Après |
|---|---|---|
| Padding card | 40px 32px | **30px 26px** |
| Headline "Tu progresses." | clamp(22, 4vw, 30px) | **clamp(18, 3vw, 24px)** |
| Big num "40%" | clamp(70, 14vw, 100px) | **clamp(56, 11vw, 80px)** |
| Meta "4/10 · 12:00" | 14/20/13px | **13/17/12px** |

Card moins imposante, plus de place pour le bilan.

### Animation "Amélie réfléchit" → scroll-triggered

**Problème** : l'animation démarrait dans `renderScore()` et durait 3s. Si l'utilisateur mettait >3s à scroller, il voyait déjà le bilan final sans voir l'animation.

**Fix** : `IntersectionObserver` sur `#amelieThinking` avec `threshold: 0.25`. L'animation + l'API call démarrent **quand le bloc entre à 25% dans le viewport**.

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

### Alignement de tous les blocks (largeur uniforme)

**Avant** : `.score-bravo` + `.score-card` héritaient de `.score-card-wrap` (560px) mais `#anonReportSlot` et `.amelie-thinking` avaient leur propre `max-width: 460px` → bilan visiblement plus étroit que les cards au-dessus.

**Fix** : suppression des `max-width: 460px` sur `#anonReportSlot` et `.amelie-thinking` → héritent du parent (560px). **Tous les blocks fin de quiz ont maintenant la même largeur** :
- Bravo (stakes)
- Score card (40% + meta)
- Amélie réfléchit (animation)
- Bilan Amélie (analyse)
- Card CTA "Ton bilan personnalisé t'attend"

---

## 🏠 6. Homepage — ajustements

### Cards "Comment ça marche" — largeur et hauteur égales

**Avant** : `grid-template-columns: repeat(3, 1fr)` donnait largeur égale en théorie, mais la card 1 avait un CTA "Commencer →" en plus, causant des hauteurs différentes.

**Fix** :
- `grid-template-columns: repeat(3, minmax(0, 1fr))` → force l'égalité de largeur même si un contenu est large
- `.step-card` : `display:flex; flex-direction:column; height:100%` → cards de hauteur égale (stretch)
- `.step-card p { flex:1 }` → le paragraphe prend l'espace restant, CTA aligné en bas quand il existe

Résultat : **3 cards parfaitement uniformes** (largeur et hauteur).

---

## 📋 7. Récap modifications visuelles par zone

### 🟢 Éléments passés au VERT FLASH (ex-violet)

**CTA principaux** (bouton unique par popup) :
- "Passer à Focus Illimité →" (paywall popups)
- "Passer au plan Focus Illimité" (ask-gate)
- "Passer à Focus Illimité →" (card fin de quiz → signup gate)
- "Demander à Amélie" (inline dans feedback quiz)

**Checkmarks ✓** :
- `.pw-feats li::before` (popups paywall)
- `.report-unlock-feats li::before` (card fin de quiz)
- Gradient : `linear-gradient(135deg, #00E676 0%, #00B85E 100%)`

**Badges "ILLIMITÉ"** :
- Year cards / year switcher / dossier lock badges
- Gradient `#00E676 → #00B85E` + crown SVG blanc

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
- `.report-unlock-feats li strong` : `#0E5552`
- Accent titre "ton assistante médicale" : gradient `#0E5552 → #1B6E68`
- Border shadows : `rgba(14,85,82,X)`

---

## 🚨 8. Vérifications à faire après migration prod

1. **Contraste AA** : vérifier que `#00E676` sur fond blanc respecte AA pour le texte (ratio ≥ 4.5:1). Pour les boutons avec texte blanc, préférer `#00B85E` en background.
2. **Cohérence** : s'assurer qu'aucun élément décoratif n'utilise le vert flash (réservé action uniquement).
3. **SVG crown data URIs** : vérifier que les `fill="%2300B85E"` sont correctement percent-encodés dans tous les fichiers.
4. **Couronne centrée dans les cercles** : sur mobile, la couronne doit être SVG à ~50% du wrap (ex: 26×26 dans 52×52), pas remplir tout le cercle.
5. **IntersectionObserver** : vérifier le fallback pour vieux navigateurs (démarrer l'animation directement).
6. **Popups centrés** : `align-items:center` partout, pas de `flex-end` résiduel.
7. **Blocks fin de quiz** : tous doivent avoir la même largeur max (560px via `.score-card-wrap`).
8. **Cards "Comment ça marche"** : 3 cards doivent être parfaitement uniformes (flex + height:100%).

---

## 📦 9. Historique chronologique des 17 commits post-swap

| # | Hash | Titre |
|---|---|---|
| 1 | `8b18848` | Palette Clinical Green (violet → vert flash) — **fondation** |
| 2 | `6ebabf7` | Doc migration Clinical Green |
| 3 | `530c5c1` | Popups : cadenas → couronne + fix mobile |
| 4 | `2061447` | Popup ask-Amélie centrée mobile |
| 5 | `fe0d53d` | Popup ask-Amélie texte réduit (Hick) |
| 6 | `2c3174f` | Popup ask-Amélie width/padding |
| 7 | `382541c` | Padding +4px |
| 8 | `9702818` | Padding 32px (final) |
| 9 | `eefb9ed` | Couronne FA classique (tentative abandonnée) |
| 10 | `9743a4b` | Couronne uniforme simple 3-peaks (finale) |
| 11 | `f4e040e` | Couronne verte `#00B85E` |
| 12 | `590dc96` | Fin quiz couronne verte + card CTA |
| 13 | `d9b3e80` | CTA fin quiz → signup gate + suppression doublon |
| 14 | `d6563db` | Animation scroll-into-view |
| 15 | `176e7f2` | Score card réduite 20% |
| 16 | `c881328` | Doc Clinical Green mise à jour |
| 17 | `1518901` | 3 cards "Comment ça marche" même largeur/hauteur |
| 18 | `10a090e` | Tous les blocks fin de quiz alignés même largeur |

---

## 📄 Fichiers associés

- `CHANGELOG-2026-04-22.md` : historique complet de la journée
- `MIGRATION-PROD.md` : guide global avec visuels ASCII + matrice des popups
- **Ce document** : focus Clinical Green + tous les ajustements post-swap

Repo GitHub : `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`.
