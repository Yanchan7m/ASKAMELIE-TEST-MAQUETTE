# Guide de migration final — Ask Amélie (23 avril 2026)

Document complet reprenant la **palette finale "Premium Clinical" 4 teintes** + **accents salmon logo** + **tous les ajustements UX** depuis le passage violet → vert. À transmettre pour porter les changements de la maquette statique vers la codebase prod.

- **Fichiers source** : `index.html`, `ecn-focus.html`
- **40 commits** sur la branche principale depuis le palette swap
- **Date** : 22-23 avril 2026
- **Repo** : `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`

---

## 📋 Table des matières

1. [Palette couleur "Premium Clinical" (4 teintes + accent logo)](#palette)
2. [Règles d'ouverture des popups + matrice CTA](#popups)
3. [Couronne unifiée partout](#couronne)
4. [Homepage : hero, stats, sections](#homepage)
5. [Footer structure multi-colonnes](#footer)
6. [Logo wordmark Ask + amelie](#logo)
7. [Fin de quiz : card unifiée + sticky](#finquiz)
8. [Règle critique : signup d'abord, Stripe ensuite](#signupfirst)
9. [Bugs corrigés](#bugs)
10. [Commande perl reproductible](#perl)
11. [Vérifications post-migration](#verif)

---

<a id="palette"></a>
## 1. 🎨 Palette couleur "Premium Clinical" — 4 teintes + accent

### Les 4 teintes fonctionnelles

| Rôle | Couleur | Hex | Usage |
|---|---|---|---|
| **Vert profond** (brand) | Teal profond | `#0F6B5B` | Titres, couronne, coches bullets, logo, bordures principales, texte fort |
| **Vert flash** (action) | Vert vif | `#19D36B` (light) / `#13B05A` (dark) | CTA uniquement, badges ILLIMITÉ, signaux de conversion |
| **Vert sauge** (support) | Menthe douce | `#B5CDB4` / `#DCE8DB` (soft) | Cards actives, états légers, backgrounds tintés secondaires |
| **Beige chaud** (ambiance) | Pierre crème | `#EEE7DC` / `#DDD3C3` (border) | Encadrements, surlignages, fonds secondaires structurants |

### Accent identité — couleur du logo

| Accent | Hex | Usage minimaliste |
|---|---|---|
| **Salmon/beige logo** | `#E89C6E` | Surligneur "+300 places" (translucide `.45`), border-top dashed fin de quiz, progress bar gradient (teal → salmon) |

### Variables de support

```css
:root{
  --bg:#FAF8F3;           /* Fond général */
  --fg:#163A3A;           /* Texte principal */
  --fg-soft:#527A70;      /* Texte secondaire sage */
  --primary:#0F6B5B;      /* Vert profond brand */
  --primary-darkest:#1B4D3E;
  --accent:#E89C6E;       /* Salmon logo */
  --sage:#DDD3C3;         /* Border beige */
  --sage-soft:#EEE7DC;    /* Fond beige */
  --sauge:#B5CDB4;        /* Vert sauge */
  --sauge-soft:#DCE8DB;   /* Vert sauge pâle */
}
```

### Règle d'hiérarchie

1. **Vert profond** reste dominant (80% de l'UI)
2. **Vert flash** strictement réservé à l'action (max 1 actif par écran)
3. **Vert sauge** pour adoucir, jamais concurrence le CTA
4. **Beige** pour structurer sans prendre le poids visuel principal
5. **Salmon** en touches signature (rappel logo), jamais en dominante

---

<a id="popups"></a>
## 2. 🪟 Règles d'ouverture popups — Matrice CTA

### Popup paywall ouvre quand :

| Action user | Context | Titre résultant |
|---|---|---|
| Clic année verrouillée 2017-2022 (homepage) | `year-2017` etc. | `ECN 2017` |
| Clic année verrouillée 2023-2025 (homepage) | `year-2023` etc. | `EDN 2023` |
| Clic année verrouillée (year switcher dashboard) | `year-XXXX` | `ECN/EDN XXXX` |
| Clic dossier verrouillé 2016 (non-free) | `exam-locked` | Cette épreuve est réservée |
| Clic exam verrouillé (DCP 2+, QI, LCA) | `exam-locked` | Cette épreuve est réservée |
| Clic "voir plus" correction verrouillée | `explanation-locked` | Correction complète réservée |
| Clic status pill "Compte gratuit" dashboard | `nav` | Accès Focus Illimité |
| Clic bouton nav "Focus Illimité" topbar | `nav` | Accès Focus Illimité |
| Post-signup | `post-signup` | 🎉 Bienvenue chez Ask Amélie |

### Popup ask-gate (Demander à Amélie)

| Action user | Popup |
|---|---|
| Clic bouton "Demander à Amélie" dans feedback quiz | ✅ Ask-gate |

### Popup signup gate (firstQGate)

| Action user | Mode |
|---|---|
| Clic "Passer à Focus Illimité" dans paywall | `signup` (premiumIntent=true) |
| Clic "Passer à Focus Illimité" fin de quiz | `signup` |
| Clic "Se connecter" (lien bas paywall) | `login` |

---

<a id="couronne"></a>
## 3. 👑 Couronne SVG unifiée partout

### SVG standard

```html
<svg viewBox="0 0 24 24" fill="currentColor">
  <path d="M3 17h18l-1.5-9L15 12 12 5 9 12 4.5 8 3 17zm0 2h18v2H3v-2z"/>
</svg>
```

### Emplacements (9 endroits)

| Lieu | Sélecteur | Taille | Couleur |
|---|---|---|---|
| Popup ask-gate | `.ask-gate-crown svg` | 32×32 dans cercle 64×64 | `#0F6B5B` (vert profond) |
| Popup paywall (ecn-focus) | `.pw-lock svg` | 28×28 dans cercle 56×56 | `#0F6B5B` |
| Popup paywall (index) | `.pw-lock-wrap svg` | 28×28 dans cercle 56×56 | `#0F6B5B` |
| Card CTA fin de quiz | `.report-unlock-crown svg` | 28×28 dans cercle 56×56 | `#0F6B5B` |
| Badge year card verrouillée | `.year-card.locked::after` data URI | 10×10 | Blanc sur gradient flash |
| Badge year switcher | `.ys-card.locked::after` data URI | 9×9 | Blanc sur gradient flash |
| Badge dossier verrouillé | `.dossier-card .lock-badge::before` data URI | 11×11 | Blanc sur gradient flash |
| Icône "voir plus" | `.q-voir-plus::before` data URI | 14×14 | Vert profond |
| Dossier locked inline JS | `lockBadge <svg>` inline | 16×16 | Vert profond `#0F6B5B` |

### Cercle d'accueil des couronnes principales

```css
width: 56px; height: 56px; border-radius: 50%;
background: #EEE7DC; border: 1px solid #DDD3C3;
```

### Cadenas 🔒 conservé UNIQUEMENT sur "Paiement sécurisé"
Partout ailleurs, couronne.

---

<a id="homepage"></a>
## 4. 🏠 Homepage

### Bandeau top
> 🎯 **Mieux classé** = plus de choix sur ta ville et ta spé

### Hero
- **Pas d'eyebrow** (ex "Plateforme n°1 EDN" retirée)
- H1 : `Teste un dossier <em>gratuitement</em>.<br>Vois la différence.`
- Sub : "Toutes les annales EDN/ECN depuis 2016 en conditions réelles. Raisonnement clinique détaillé par des médecins spécialistes : pas juste la bonne lettre."
- CTA vert teal : "Commencer le dossier gratuit →"

### Stats row — **card beige**
- Encadré dans une card `background:#EEE7DC; border:1px solid #DDD3C3; border-radius:14px`
- 4 chiffres : 10 années · 850+ dossiers · 13 spécialités · 100% corrigé médecins

### Social proof
- Texte : "Rejoint par **+2034 D4** cette année · ⭐ 4.9/5 sur 400+ avis"
- 4 avatars via `pravatar.cc/80?img=5/15/23/33` (URLs stables, visages jeunes adultes)

### Section "Comment ça marche" — **fond beige full-width**
- Fond section beige `#EEE7DC` avec bordures top/bottom
- 3 cards **blanches** pour contraster
- Sous-titre : "En 3 étapes, découvre ton niveau réel..."
- 3 cards parfaitement uniformes (`grid-template-columns: repeat(3, minmax(0, 1fr))` + `display:flex; height:100%`) :
  1. **Teste un vrai dossier.** - Découvre ton niveau sur un cas concret
  2. **Vois ce que ça t'apporte.** - Compare avec la correction d'un spécialiste
  3. **Transforme ça en choix.** - Augmente tes chances de garder l'option

### Year cards verrouillées
- **Badge "ILLIMITÉ" vert flash** avec couronne SVG blanche (même design que dossier cards)
- Card opacity:1 (plus de fade)
- Hover tip `pointer-events:none` (évite bug de clic)

### Section "Amélie te connaît" (Dashboard preview)
- **Pill "SUIVI INTELLIGENT" retiré** (faisait trop IA)

### Section "Pricing"
- **Pill "TARIF UNIQUE" retiré**

### Section "Témoignages"
- **Pill "RÉSULTATS" retiré**
- **3 témoignages étudiants** (Dr. S. Martin retiré, cardiologue correcteur) :
  - Léa D. - D4 Paris Descartes
  - Antoine M. - D4 Bordeaux (étudiant blanc, img=12)
  - Sofia R. - D4 Toulouse (étudiante hispanique, img=47)

---

<a id="footer"></a>
## 5. 🦶 Footer structure multi-colonnes (inspiration Docgenda)

```
┌─────────────────────────────────────────────────────────────┐
│ ● Ask Amélie         PRODUIT   RESSOURCES  ENTREPRISE LÉGAL│
│ Ask Amélie EDN :     Démo      Centre aide À propos   M.L. │
│ l'entraînement…      Tarifs    Blog        Contact    Conf.│
│                      Témoignages Guides                 CGV│
│                      FAQ                               Cook│
│─────────────────────────────────────────────────────────────│
│ ✓ Corrections médicales · médecins spécialistes            │
│ ✓ Hébergement conforme RGPD · données en France            │
│ ✓ Édité par Askamelie · Paris, France                      │
│─────────────────────────────────────────────────────────────│
│ © 2026 Ask Amélie     Signaler erreur : contact@…com       │
└─────────────────────────────────────────────────────────────┘
```

- 5 colonnes desktop, 2 cols tablet (<900px), 1 col mobile (<540px)
- **Pas d'Instagram/TikTok** (retirés à la demande)
- Zone "trust" avec 3 checkmarks verts
- Copyright + signalement en bas

---

<a id="logo"></a>
## 6. 🎨 Logo wordmark "Ask amelie"

```html
<a href="index.html" class="brand">
  <span class="mark">A</span>  <!-- display:none via CSS -->
  <span class="title">Ask<em>amelie</em></span>
</a>
```

CSS :
```css
.brand{
  display:inline-flex;align-items:baseline;
  font-family:'Libre Baskerville',Georgia,serif;
  font-size:22px;font-weight:800;letter-spacing:-.02em;
  text-decoration:none;line-height:1;
}
.brand .mark{display:none}
.brand .title{color:#0F6B5B}           /* "Ask" vert profond */
.brand .title em{font-style:normal;color:#E89C6E;font-weight:700}  /* "amelie" salmon */
```

**Wordmark 2 tons** comme sur le screenshot promo.askamelie :
- `Ask` vert profond `#0F6B5B`
- `amelie` salmon `#E89C6E`

---

<a id="finquiz"></a>
## 7. 🎓 Fin de quiz — card unifiée + sticky CTA

### Card unifiée `.score-unified` (fusion score-bravo + score-card)

Structure (un seul bloc, plus 2 cards séparées) :

```
┌────────────────────────────────────────┐
│  [ECN 2016] Dossier démo cardio        │  ← header
│                                         │
│         Bravo 🎉                        │  ← title
│   Tu viens de faire ton premier test…   │  ← intro
│                                         │
│        Tu progresses.                   │  ← headline (vert flash)
│           40%                           │  ← bignum (vert profond)
│    4/10 bonnes réponses · 12:00 temps   │  ← meta
│                                         │
│  ┌────────────────────────────────┐    │
│  │ 🎯 Aux EDN, chaque place compte │    │  ← stakes box
│  │ Ton rang détermine...           │    │
│  │ [EDN 60% · ECOS 30% · Parc 10%] │    │  ← breakdown dashed vert flash
│  └────────────────────────────────┘    │
│                                         │
│  Chez Ask Amélie, +300 places en…       │  ← stat avec salmon highlight
│                                         │
│  ╌╌╌╌╌╌╌ dashed SALMON ╌╌╌╌╌╌╌╌         │
│  ⏱ Tu as bossé 5 ans :                  │  ← anchor (vert profond)
└────────────────────────────────────────┘
```

**CSS** :
- `background: #fff` (blanc uniforme comme les autres blocks)
- `border: 1px solid #DDD3C3` (bordure beige)
- Bordures latérales tonales selon score : good=sauge, mid=beige, low=rose
- Shadow : `0 8px 28px rgba(15,107,91,.08)`

### 3 blocks harmonisés (tous blancs + bordure beige)

1. `.score-unified` - résumé quiz
2. `.amelie-thinking` - "Amélie réfléchit" animation
3. `.report-card` - "Analyse d'Amélie" bilan
4. `.report-unlock` - "Ton bilan personnalisé t'attend" CTA

Même style partout :
```css
background: #fff;
border: 1px solid #DDD3C3;
border-radius: 20px;
box-shadow: 0 8px 28px rgba(15,107,91,.08);
```

### Animation "Amélie réfléchit" — scroll-triggered

- IntersectionObserver sur `#amelieThinking` avec `threshold: 0.25`
- L'animation + l'API call démarrent **quand le bloc entre à 25% dans le viewport**
- Fallback si IntersectionObserver absent : démarrage direct

### Scroll-to-top au render

Au début de `renderScore()` :
```js
try {
  var ov = document.getElementById('examOverlay');
  if (ov) ov.scrollTo({ top: 0, behavior: 'auto' });
  window.scrollTo({ top: 0, behavior: 'auto' });
} catch(e){}
```

L'utilisateur atterrit en haut du bilan quand le quiz se termine.

### Sticky CTA fin de quiz (non-premium uniquement)

```html
<div class="score-sticky-cta visible" id="scoreStickyCta">
  <div class="score-sticky-cta-inner">
    <div class="score-sticky-cta-txt">Ton bilan personnalisé t'attend
      <span class="muted">Débloque 850+ corrigés et tous tes résultats</span>
    </div>
    <button class="score-sticky-cta-btn" onclick="showFirstQuestionGate(true)">
      Passer à Focus Illimité →
    </button>
  </div>
</div>
```

CSS clé :
```css
.score-sticky-cta{
  position: fixed; bottom: 0; left: 0; right: 0;
  z-index: 10500;  /* IMPORTANT : au-dessus de examOverlay (9999) */
  background: #fff; border-top: 1px solid #DDD3C3;
  padding: 14px 20px; box-shadow: 0 -8px 28px rgba(15,107,91,.12);
  transform: translateY(110%); transition: transform .4s cubic-bezier(.16,1,.3,1);
  opacity: 0; pointer-events: none;
}
.score-sticky-cta.visible{
  transform: translateY(0); opacity: 1; pointer-events: auto;
}
```

**Mécanique JS** :
1. Sticky créée dans `renderScore()` si `!isPremium`
2. **Déplacée dans `<body>`** via `appendChild` (pour que `position:fixed` soit viewport-relative)
3. `#scoreStickyCta` **ajouté à la whitelist early-hide CSS** (pour `?auto=1`/`?dash=1`)
4. Cleanup automatique dans `exGoBack()` et `closeExam()` (retire du DOM au back)

### Accents salmon sur la fin de quiz

- Surligneur "+300 places" : `background: linear-gradient(180deg, transparent 60%, rgba(232,156,110,.45) 60%)`
- Border-top `.anchor-line` : `2px dashed #E89C6E`
- Progress bar quiz : gradient `#0F6B5B 0% → #E89C6E 100%`

---

<a id="signupfirst"></a>
## 8. ⚠️ Règle critique : signup d'abord, Stripe ensuite

**Exigence** : quand un utilisateur clique "Passer à Focus Illimité" n'importe où, il va d'abord vers la **création de compte**. Le paiement Stripe se fait après, depuis son compte.

### Implémentation

Tous les CTA Focus Illimité appellent soit :
- `showFirstQuestionGate(true)` directement (anonyme ou authed)
- `goPremium(ctx)` qui route : anon → signup gate, authed → Stripe

**Liens directs Stripe supprimés** :
- `.pw-cta-gold` dans paywall modal → `onclick="closePaywall();showFirstQuestionGate(true)"`
- `.offer-cta` section pricing → `onclick="showFirstQuestionGate(true)"`

### Exceptions (Stripe direct conservé)

| CTA | Justification |
|---|---|
| CTA Stripe **dans** popup paywall | User a déjà vu le pitch |
| CTA `.premium-cta-btn` footer | Section de conversion dédiée |
| CTA `.sticky-btn` bottom bar homepage | Identique |

---

<a id="bugs"></a>
## 9. 🐛 Bugs corrigés à vérifier côté prod

| Bug | Cause | Fix |
|---|---|---|
| Clamp 2 lignes KO sur explication verrouillée | Règle CSS non !important | `!important` + `-webkit-line-clamp:2` + `display:-webkit-box` |
| `<strong>`/`<em>` sautent sur lignes | `<li>` en flex → chaque enfant = flex item | Wrapper contenu dans `<span>` |
| Clic année verrouillée sans effet | `.tip` hover `position:absolute;inset:0` bloquait | `pointer-events:none` sur `.tip` |
| Popup voir plus KO en `?auto=1` | Whitelist early-hide ne contenait pas `#paywall` | Ajouter `:not(#paywall):not(.paywall)` |
| SVG data URI casse parsing CSS | Quotes mal encodées | Percent-encoder tout (`%20`, `%22`, `%3C`, `%3E`, `%2F`) |
| Sticky CTA fin de quiz invisible | z-index 60 < examOverlay z-index 9999 | z-index 10500 + appendChild to body + whitelist |
| Sticky CTA persiste après back | Pas de cleanup au navigateur | Remove element dans `exGoBack()` + `closeExam()` |

---

<a id="perl"></a>
## 10. 🔧 Commande perl reproductible (palette swap prod)

```bash
perl -pi -e "
  s/#A855F7/#19D36B/g;
  s/#7c3aed/#0F6B5B/g;
  s/#6B2FD6/#0A4A3E/g;
  s/#00E676/#19D36B/g;
  s/#00B85E/#0F6B5B/g;
  s/#0E5552/#0F6B5B/g;
  s/#1B6E68/#0F6B5B/g;
  s/#08403E/#0A4A3E/g;
  s/#00A050/#0A4A3E/g;
  s/rgba\(168,85,247,/rgba(25,211,107,/g;
  s/rgba\(124,58,237,/rgba(15,107,91,/g;
  s/rgba\(0,230,118,/rgba(25,211,107,/g;
  s/rgba\(0,184,94,/rgba(15,107,91,/g;
  s/rgba\(14,85,82,/rgba(15,107,91,/g;
  s/#F5EEFE/#E8F4F1/g;
  s/#EDE4FA/#C8E8D8/g;
  s/#FBF7FE/#F0F9F4/g;
  s/#D9C3F7/#9FDCBF/g;
  s/#EAF0EA/#EEE7DC/g;
  s/#C4D3C5/#DDD3C3/g;
  s/#F5F9F5/#FAF8F3/g;
  s/#FAF9F6/#FAF8F3/g;
  s/#F5F2EC/#EEE7DC/g;
  s/#19342D/#163A3A/g;
  s/%2300B85E/%230F6B5B/g;
  s/%2300E676/%2319D36B/g;
  s/%237c3aed/%230F6B5B/g;
" path/to/your/files.{html,css,scss,js,jsx,tsx,vue}

# Puis ajuster les gradients CTA pour éviter "flash→profond" moche
perl -pi -e "
  s|linear-gradient\(135deg,#19D36B 0%,#0F6B5B 100%\)|linear-gradient(135deg,#19D36B 0%,#13B05A 100%)|g;
" files.{html,css}
```

---

<a id="verif"></a>
## 11. 🚨 Vérifications post-migration

1. **Contraste AA** : `#19D36B` sur fond blanc ≥ 4.5:1 pour le texte. Sur boutons CTA avec texte blanc, préférer `#13B05A` en gradient.
2. **Cohérence** : aucun élément décoratif en vert flash (réservé action uniquement).
3. **SVG crown data URIs** : `fill="%230F6B5B"` bien percent-encodé (pas de `#`).
4. **Couronne dans cercle** : sur mobile, SVG ~50% du wrap (ex: 26×26 dans 52×52).
5. **Popups centrés** : `align-items:center` partout, pas de `flex-end` résiduel.
6. **Blocks fin de quiz** : même largeur max (560px via `.score-card-wrap`).
7. **Cards "Comment ça marche"** : uniformes (flex + `height:100%`).
8. **Sticky CTA** : z-index:10500 pour passer au-dessus de examOverlay.
9. **IntersectionObserver** : fallback pour vieux navigateurs.
10. **Logo 2 tons** : `Ask` vert profond, `amelie` salmon.

---

## 📦 Fichiers de référence

- `index.html` : homepage cible
- `ecn-focus.html` : quiz engine + fin de quiz
- `CHANGELOG-2026-04-22.md` : historique chronologique du 22 avril
- `MIGRATION-PROD.md` : guide global avec visuels ASCII + matrice popups
- `MIGRATION-COLOR-CLINICAL-GREEN.md` : détail swap violet → vert
- `DESIGN-BRIEF-NEW-HOMEPAGE.md` : brief inspiration Docgenda
- **Ce document** : guide final tout-en-un (palette + UX + bugs)

Repo GitHub : `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`.
