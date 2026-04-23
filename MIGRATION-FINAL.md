# Guide de migration final — Ask Amélie

Document complet de la refonte 22-23 avril 2026. À transmettre au dev prod pour porter les changements de la maquette statique vers la codebase prod.

- **Fichiers source** : `index.html`, `ecn-focus.html`
- **Repo** : `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`

---

## 📋 Sections

1. [Palette couleur simplifiée (4 teintes + logo)](#palette)
2. [Règles d'ouverture des popups + matrice CTA](#popups)
3. [Couronne unifiée partout](#couronne)
4. [Logo wordmark "Ask amelie"](#logo)
5. [Homepage](#homepage)
6. [Footer multi-colonnes](#footer)
7. [Fin de quiz](#finquiz)
8. [Règle critique : signup d'abord, Stripe ensuite](#signupfirst)
9. [Bugs corrigés](#bugs)
10. [Commande perl reproductible](#perl)
11. [Vérifications post-migration](#verif)

---

<a id="palette"></a>
## 1. 🎨 Palette simplifiée — 4 teintes

Après plusieurs itérations, la palette finale est volontairement restreinte pour garder une interface premium et cohérente.

| Rôle | Couleur | Hex | Usage |
|---|---|---|---|
| 🟢 **Vert profond** (brand) | Teal profond | `#0F6B5B` | Titres, couronne, coches bullets, logo, bordures principales, texte fort |
| ⚡ **Vert flash** (action) | Vert vif | `#19D36B` (light) / `#13B05A` (dark) | **CTA uniquement**, badges ILLIMITÉ, signaux de conversion |
| 🟤 **Beige chaud** (ambiance) | Pierre crème | `#EEE7DC` / `#DDD3C3` (border) | Encadrements, surlignages, fonds secondaires |
| ⚪ **Blanc** | `#fff` (cards) / `#FAF8F3` (bg général) | | Backgrounds principaux |

### Accent identité — logo uniquement

| Accent | Hex | Usage |
|---|---|---|
| **Salmon logo** | `#E89C6E` / `#E69F70` | Uniquement sur la partie "amelie" du logo + surligneurs spécifiques (ex: `.hl-beige` sur "tester") |

### Variables CSS clés

```css
:root{
  --bg:#FAF8F3;
  --fg:#163A3A;
  --fg-soft:#527A70;
  --primary:#0F6B5B;
  --primary-darkest:#1B4D3E;
  --accent:#E89C6E;
  --sage:#DDD3C3;
  --sage-soft:#EEE7DC;
}
```

### Règles d'hiérarchie (strictes)

1. **Vert profond** reste dominant (80% de l'UI)
2. **Vert flash** strictement réservé à l'action (max 1 actif par écran)
3. **Beige** pour structurer sans prendre le poids principal
4. **Salmon** : logo + highlights de mots-clés (span `.hl-beige`), pas autre chose

### Classe réutilisable pour surlignage

```css
.hl-beige{
  background: linear-gradient(180deg, transparent 60%, #E69F70 60%);
  padding: 0 3px;
}
```

Utilisable : `<span class="hl-beige">mot</span>`

---

<a id="popups"></a>
## 2. 🪟 Matrice popups — règles d'ouverture

### Popup paywall ouvre sur :

| Action user | Context | Titre résultant |
|---|---|---|
| Clic année verrouillée 2017-2022 (homepage) | `year-2017` etc. | `ECN 2017` |
| Clic année verrouillée 2023-2025 (homepage) | `year-2023` etc. | `EDN 2023` |
| Clic année verrouillée (year switcher dashboard) | `year-XXXX` | `ECN/EDN XXXX` |
| Clic dossier verrouillé 2016 (non-free) | `exam-locked` | Cette épreuve est réservée |
| Clic exam verrouillé (DCP 2+, QI, LCA) | `exam-locked` | Cette épreuve est réservée |
| Clic "voir plus" correction verrouillée | `explanation-locked` | Correction complète réservée |
| Clic status pill dashboard / bouton nav | `nav` | Accès Focus Illimité |
| Post-signup | `post-signup` | 🎉 Bienvenue chez Ask Amélie |

### Popup ask-gate (Demander à Amélie)

Ouvre sur : clic bouton "Demander à Amélie" dans feedback quiz

### Popup signup gate (firstQGate)

Ouvre sur : tous les CTA Focus Illimité (avec `premiumIntent=true`) → user crée compte puis Stripe

---

<a id="couronne"></a>
## 3. 👑 Couronne SVG unifiée

### SVG standard (24×24)

```html
<svg viewBox="0 0 24 24" fill="currentColor">
  <path d="M3 17h18l-1.5-9L15 12 12 5 9 12 4.5 8 3 17zm0 2h18v2H3v-2z"/>
</svg>
```

### 9 emplacements

| Lieu | Taille | Couleur |
|---|---|---|
| `.ask-gate-crown svg` (popup ask-Amélie) | 32×32 dans cercle 64×64 (desktop) ; 26×26 dans 52×52 (mobile) | `#0F6B5B` |
| `.pw-lock svg` (popup paywall ecn-focus) | 28×28 dans cercle 56×56 | `#0F6B5B` |
| `.pw-lock-wrap svg` (popup paywall index) | 28×28 dans cercle 56×56 | `#0F6B5B` |
| `.report-unlock-crown svg` (card fin de quiz) | 28×28 dans cercle 56×56 | `#0F6B5B` |
| `.year-card.locked::after` data URI (badges) | 10×10 | Blanc sur gradient flash |
| `.ys-card.locked::after` data URI | 9×9 | Blanc sur gradient flash |
| `.dossier-card .lock-badge::before` data URI | 11×11 | Blanc sur gradient flash |
| `.q-voir-plus::before` data URI (voir plus) | 14×14 | Vert profond |
| Dossier locked inline (JS `lockBadge <svg>`) | 16×16 | `#0F6B5B` |

### Cercle d'accueil des couronnes principales

```css
width: 56px; height: 56px; border-radius: 50%;
background: #EEE7DC; border: 1px solid #DDD3C3;
```

### Cadenas 🔒 conservé uniquement sur "Paiement sécurisé" (booking Stripe)

Partout ailleurs : couronne.

---

<a id="logo"></a>
## 4. 🎨 Logo wordmark "Ask amelie"

```html
<a href="index.html" class="brand">
  <span class="mark">A</span>  <!-- display:none via CSS -->
  <span class="title">Ask<em>amelie</em></span>
</a>
```

```css
.brand{
  display:inline-flex; align-items:baseline;
  font-family:'Libre Baskerville',Georgia,serif;
  font-size:22px; font-weight:800; letter-spacing:-.02em;
  text-decoration:none; line-height:1;
}
.brand .mark{display:none}
.brand .title{color:#0F6B5B}
.brand .title em{font-style:normal;color:#E89C6E;font-weight:700}
```

**Wordmark 2 tons** (comme sur promo.askamelie.com) :
- `Ask` vert profond `#0F6B5B`
- `amelie` salmon `#E89C6E`

---

<a id="homepage"></a>
## 5. 🏠 Homepage `index.html`

### Bandeau top
> 🎯 **Mieux classé** = plus de choix sur ta ville et ta spé

### Hero
- Pas d'eyebrow
- H1 : `Teste un dossier <em>gratuitement</em>.<br>Vois la différence.`
- Sub : "Toutes les annales EDN/ECN depuis 2016 en conditions réelles. Raisonnement clinique détaillé par des médecins spécialistes : pas juste la bonne lettre."
- CTA vert teal : "Commencer le dossier gratuit →"

### Stats row dans card beige
- `background:#EEE7DC; border:1px solid #DDD3C3; border-radius:14px`
- 4 chiffres : 10 années · 850+ dossiers · 13 spécialités · 100% corrigé médecins

### Social proof
- Texte : "Rejoint par **+2034 D4** cette année · ⭐ 4.9/5 sur 400+ avis"
- 4 avatars via `pravatar.cc/80?img=5/15/23/33` (URLs stables)

### Section "Comment ça marche" (fond beige full-width)
Sous-titre : "En 3 étapes, découvre ton niveau réel…"

3 cards **blanches** uniformes (`grid-template-columns: repeat(3, minmax(0, 1fr))` + `height:100%`) :
1. **Teste un vrai dossier.** - Découvre ton niveau sur un cas concret
2. **Vois ce que ça t'apporte.** - Compare avec la correction d'un spécialiste
3. **Transforme ça en choix.** - Augmente tes chances pour l'internat

### Year cards verrouillées
- Badge **"ILLIMITÉ" vert flash** avec couronne SVG blanche
- Card opacity:1 (plus de fade)
- `.tip` hover avec `pointer-events:none`

### Sections avec pills retirées
Les pills kickers "SUIVI INTELLIGENT", "TARIF UNIQUE", "RÉSULTATS" supprimés au-dessus des H2 (faisaient "trop IA").

### Témoignages (3 avis étudiants D4)
- **Léa D.** - D4 Paris Descartes
- **Antoine M.** - D4 Bordeaux (étudiant blanc, pravatar img=12)
- **Sofia R.** - D4 Toulouse (étudiante hispanique, pravatar img=47)

Dr. S. Martin (cardiologue correcteur) retiré — pas pertinent pour la cible D4.

---

<a id="footer"></a>
## 6. 🦶 Footer multi-colonnes

Structure 5 colonnes desktop, 2 cols tablet (<900px), 1 col mobile (<540px) :

```
┌──────────────────────────────────────────────────────────────┐
│ ● Ask Amélie         PRODUIT    RESSOURCES  ENTREPRISE  LÉGAL│
│ Ask Amélie EDN :     Démo       Centre aide À propos    M.L.│
│ l'entraînement…      Tarifs     Blog        Contact     Conf│
│                      Témoignages Guides                  CGV│
│                      FAQ                                 Cook│
│──────────────────────────────────────────────────────────────│
│ ✓ Corrections médicales · médecins spécialistes             │
│ ✓ Hébergement conforme RGPD · données en France             │
│ ✓ Édité par Askamelie · Paris, France                       │
│──────────────────────────────────────────────────────────────│
│ © 2026 Ask Amélie     Signaler erreur : contact@…com        │
└──────────────────────────────────────────────────────────────┘
```

- Pas d'Instagram ni TikTok
- Zone "trust" avec 3 checkmarks verts
- Copyright + signalement email

---

<a id="finquiz"></a>
## 7. 🎓 Fin de quiz

### Card unifiée `.score-unified` (fusion score-bravo + score-card)

Structure (un seul bloc) :

```
┌────────────────────────────────────────┐
│  [ECN 2016] Dossier démo cardio        │
│         Bravo 🎉                        │
│   Tu viens de faire ton premier test…   │
│                                         │
│        Tu progresses.                   │  ← headline vert flash
│           40%                           │  ← bignum vert profond
│    4/10 bonnes réponses · 12:00 temps   │
│                                         │
│  ┌────────────────────────────────┐    │
│  │ 🎯 Aux EDN, chaque place compte │    │
│  │ Ton rang détermine…             │    │
│  │ [EDN 60% · ECOS 30% · Parc 10%] │    │  ← dashed vert flash
│  └────────────────────────────────┘    │
│                                         │
│  Chez Ask Amélie, +300 places en…       │  ← surligneur beige
│                                         │
│  ╌╌╌╌╌╌╌╌ beige dashed ╌╌╌╌╌╌╌╌╌        │
│  ⏱ Tu as bossé 5 ans :                  │
└────────────────────────────────────────┘
```

**CSS** : fond blanc `#fff`, bordure `#DDD3C3`, shadow `rgba(15,107,91,.08)`, border-radius 20px.

### 3 blocks harmonisés (tous blancs + bordure beige)

1. `.score-unified` - résumé quiz
2. `.amelie-thinking` - animation "Amélie réfléchit"
3. `.report-card` - "Analyse d'Amélie" bilan
4. `.report-unlock` - "Ton bilan personnalisé t'attend" CTA

Même style :
```css
background: #fff;
border: 1px solid #DDD3C3;
border-radius: 20px;
box-shadow: 0 8px 28px rgba(15,107,91,.08);
```

### Animation "Amélie réfléchit" — hard cap 4s

- Durée fixe **4 secondes** maximum
- Appel API en parallèle, render au bout de 4s avec :
  - Réponse API si disponible
  - Fallback local sinon

### Scroll-to-top automatique

Au début de `renderScore()` :
```js
try {
  var ov = document.getElementById('examOverlay');
  if (ov) ov.scrollTo({ top: 0, behavior: 'auto' });
  window.scrollTo({ top: 0, behavior: 'auto' });
} catch(e){}
```

### Sticky CTA fin de quiz (non-premium)

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

**Points clés techniques** :
- `position: fixed; bottom: 0; left: 0; right: 0`
- `z-index: 10500` (au-dessus de examOverlay qui est à 9999)
- Déplacée dans `<body>` via `appendChild` pour que position:fixed soit viewport-relative
- Ajoutée à la whitelist `early-hide` CSS (pour `?auto=1`/`?dash=1`)
- Cleanup dans `exGoBack()` et `closeExam()` pour éviter qu'elle persiste

---

<a id="signupfirst"></a>
## 8. ⚠️ Règle critique : signup d'abord, Stripe ensuite

**Exigence** : quand un utilisateur clique "Passer à Focus Illimité" n'importe où, il est redirigé d'abord vers **la création de compte**. Le paiement Stripe se fait après, depuis son compte.

### Implémentation

Tous les CTA Focus Illimité appellent :
- `showFirstQuestionGate(true)` directement (anonyme ou authed)
- Ou `goPremium(ctx)` qui route : anon → signup, authed → Stripe

**Liens directs Stripe supprimés** :
- `.pw-cta-gold` paywall modal → `onclick="closePaywall();showFirstQuestionGate(true)"`
- `.offer-cta` section pricing → `onclick="showFirstQuestionGate(true)"`

### Exceptions (Stripe direct OK)
- CTA Stripe à l'intérieur d'une popup paywall (user a vu le pitch)
- CTA `.premium-cta-btn` footer
- CTA `.sticky-btn` bottom bar homepage

---

<a id="bugs"></a>
## 9. 🐛 Bugs corrigés

| Bug | Cause | Fix |
|---|---|---|
| Clamp 2 lignes KO sur explication verrouillée | CSS non !important | `!important` + `-webkit-line-clamp:2` + `display:-webkit-box` |
| `<strong>`/`<em>` sautent sur lignes | `<li>` en flex → enfants = flex items | Wrapper contenu dans `<span>` |
| Clic année verrouillée sans effet | `.tip` hover absolute inset:0 bloquait | `pointer-events:none` sur `.tip` |
| Popup voir plus KO en `?auto=1` | Whitelist early-hide ne contenait pas `#paywall` | Ajouter `:not(#paywall):not(.paywall)` |
| SVG data URI casse parsing CSS | Quotes mal encodées | Percent-encoder tout (`%20`, `%22`, `%3C`, `%3E`, `%2F`) |
| Sticky CTA fin de quiz invisible | z-index 60 < examOverlay 9999 | z-index 10500 + appendChild body + whitelist |
| Sticky CTA persiste après back | Pas de cleanup au navigateur | Remove dans `exGoBack()` + `closeExam()` |

---

<a id="perl"></a>
## 10. 🔧 Commande perl reproductible

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

# Gradients CTA ajustés post-swap :
perl -pi -e "
  s|linear-gradient\(135deg,#19D36B 0%,#0F6B5B 100%\)|linear-gradient(135deg,#19D36B 0%,#13B05A 100%)|g;
" path/to/your/files.{html,css}
```

---

<a id="verif"></a>
## 11. 🚨 Vérifications post-migration

1. **Contraste AA** : `#19D36B` sur fond blanc ≥ 4.5:1 pour texte. Sur boutons CTA texte blanc, préférer `#13B05A` en gradient.
2. **Cohérence** : aucun élément décoratif en vert flash (réservé action uniquement).
3. **SVG crown data URIs** : `fill="%230F6B5B"` bien percent-encodé.
4. **Couronne dans cercle** : sur mobile, SVG ~50% du wrap (ex: 26×26 dans 52×52).
5. **Popups centrés** : `align-items:center` partout, pas de `flex-end` résiduel.
6. **Blocks fin de quiz** : même largeur max (560px via `.score-card-wrap`).
7. **Cards "Comment ça marche"** : uniformes (flex + `height:100%`).
8. **Sticky CTA** : z-index:10500 pour passer au-dessus de examOverlay.
9. **Animation thinking** : hard cap 4s.
10. **Logo 2 tons** : `Ask` vert profond, `amelie` salmon.

---

## 📦 Fichiers associés

- `index.html` : homepage
- `ecn-focus.html` : quiz engine + fin de quiz
- `CHANGELOG-2026-04-22.md` : historique chronologique du 22 avril
- `MIGRATION-PROD.md` : guide global initial avec visuels ASCII
- `MIGRATION-COLOR-CLINICAL-GREEN.md` : détail swap violet → vert
- `DESIGN-BRIEF-NEW-HOMEPAGE.md` : brief inspiration Docgenda
- **Ce document** : guide final tout-en-un

Repo GitHub : `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`.
