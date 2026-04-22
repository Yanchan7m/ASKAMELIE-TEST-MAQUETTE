# Guide de migration vers la prod — 22 avril 2026

Document à transmettre au dev de la vraie codebase pour porter les changements faits sur la maquette statique `ASKAMELIE-TEST-MAQUETTE` vers la version prod dynamique.

**Maquette source** : https://ecn.askamelie.com (statique, 2 fichiers HTML)
**Fichiers touchés** : `index.html`, `ecn-focus.html`
**Commits** : 43 commits sur `main` du 2026-04-22 (voir `CHANGELOG-2026-04-22.md` pour l'historique chronologique)

---

## 1. Rebranding complet `Premium` → `Focus Illimité`

### À faire en prod
Rechercher/remplacer sur tous les textes user-facing :
- `Premium` → `Focus Illimité`
- `l'Illimité` → `le Focus Illimité`
- `à l'Illimité` → `au Focus Illimité`

### Grammaire à surveiller
Après rename, vérifier les constructions `à le` / `de le` : doivent devenir `au` / `du`.

### Fichiers concernés sur la maquette
`index.html`, `ecn-focus.html` (templates, tooltips, CTA, paywall copy, toasts, confirmations Stripe)

### ⚠️ Ne pas toucher
- Identifiants techniques : `_IS_PREMIUM`, `goPremium()`, `trackPremiumClick()`, `premium-active` (CSS class), etc.
- Les variables / flags internes gardent leur nom.

---

## 2. Nettoyage des em-dashes `—`

Les em-dashes dans le texte user-facing sonnent "IA généré". Remplacer par `:` ou `-` selon le contexte :

- `"2016 — 2025"` → `"2016-2025"` (date ranges)
- `" — "` dans les descriptions → `" : "` ou juste `". "`

Garder les `—` uniquement dans :
- Les commentaires de code (non user-facing)
- Les placeholders "pas de valeur" (ex: `<td>—</td>` dans des tableaux de stats)

---

## 3. Palette couleur harmonisée

### Règle globale

| Usage | Couleur | Hex |
|---|---|---|
| Branding général, info, borders | Vert teal | `#0E5552` / `#1B6E68` |
| Backgrounds pâles, pills info | Vert sage | `#EAF0EA` / `#C4D3C5` / `#F5F9F5` |
| Action premium (CTA, checkmarks, badges verrouillage) | Violet | `#A855F7` / `#7c3aed` |
| Backgrounds violet pâle | Lavande | `#F5EEFE` / `#D9C3F7` |
| Highlights text (ex: "+300 places") | Violet | `rgba(168,85,247,.3)` (surligneur) |

### Supprimer partout le doré / gold
Les anciennes classes avec `#F5C542`, `#D4A017`, `#F59E0B`, `#D97706`, `#5B3F00` sont remplacées :
- Si CTA ou lien premium → violet
- Si décoration / badge → vert
- Si stats neutres (ex: warning bar orange sur dashboard) → OK à garder

### Couronne dans les badges "ILLIMITÉ"
Remplacer l'emoji `👑` par un SVG vectoriel :

```html
<svg viewBox="0 0 24 24" fill="currentColor">
  <path d="M3 17h18l-1.5-9L15 12 12 5 9 12 4.5 8 3 17zm0 2h18v2H3v-2z"/>
</svg>
```

Inliné en data URI dans `background-image` sur les pseudo-éléments `::after`/`::before`. Percent-encoder entièrement pour éviter les bugs de parsing CSS (espaces `%20`, quotes `%22`, chevrons `%3C/%3E`).

---

## 4. Popup paywall (modal verrouillage)

### Structure simplifiée

```html
<div class="paywall" id="paywall">
  <div class="paywall-box">
    <button class="pw-close">×</button>
    <div class="pw-lock"> <!-- icône cadenas SVG vert dans cercle vert pâle --> </div>
    <div class="pw-card">
      <h3 id="pwTitle">ECN 2018</h3> <!-- dynamique selon contexte -->
      <p class="pw-sub" id="pwSub">Accessible uniquement via <strong>Focus Illimité</strong>.</p>
      <ul class="pw-feats">
        <li><span><strong>Toutes les années</strong> d'annales (2016-2025)</span></li>
        <li><span><strong>Tes corrigés complets</strong> : Amélie t'explique chaque question</span></li>
        <li><span><strong>Ton bilan personnalisé</strong> détaillé après chaque dossier</span></li>
        <li><span><strong>+300 places en moyenne</strong> au classement pour nos élèves</span></li>
      </ul>
      <a class="pw-cta-gold">Passer à Focus Illimité →</a>
      <button class="pw-learn-more">Plus tard</button>
    </div>
  </div>
</div>
```

**Important** : wrapper le contenu des `<li>` dans un `<span>` pour éviter que `<strong>` / `<em>` deviennent des flex items séparés sur des lignes distinctes.

### Titres contextuels dans `openPaywall(context)`

| Context | Titre | Sub |
|---|---|---|
| `year-2017` (etc.) | `ECN 2017` | Accessible uniquement via Focus Illimité |
| `year-2023` et + | `EDN 2023` | Idem |
| `bilan-locked` | 🔒 Ton bilan personnalisé | Pitch sur bilan détaillé |
| `explanation-locked` | 🔒 Correction complète réservée | Pitch sur corrigés Amélie |
| `exam-locked` / `qi-locked` | 🔒 Cette épreuve est réservée | Pitch DCP/QI/LCA |
| `post-signup` | 🎉 Bienvenue chez Ask Amélie | Upsell post-signup |
| default | 🔒 Accès Focus Illimité | Pitch général |

### CSS clé

```css
.paywall{position:fixed;inset:0;z-index:10800;background:rgba(15,23,42,.72);
  backdrop-filter:blur(10px);display:none;align-items:center;justify-content:center;padding:24px}
.paywall.open{display:flex;animation:fIn .25s ease}
.paywall-box{background:#fff;border-radius:22px;padding:32px 26px 20px;
  max-width:460px;width:100%;box-shadow:0 30px 80px rgba(14,85,82,.3)}
.pw-feats li{display:flex;align-items:flex-start;gap:10px;padding:10px 13px;
  background:#EAF0EA;border:1px solid #C4D3C5;border-radius:11px;font-size:13px}
.pw-feats li::before{content:'✓';width:20px;height:20px;border-radius:50%;
  background:linear-gradient(135deg,#A855F7 0%,#7c3aed 100%);color:#fff}
.pw-cta-gold{background:linear-gradient(135deg,#A855F7 0%,#7c3aed 100%);color:#fff;
  border-radius:14px;padding:15px 22px;width:100%;box-shadow:0 10px 28px rgba(168,85,247,.42)}
```

### ⚠️ Piège à éviter : whitelist early-hide CSS

Certains modes d'affichage (`?auto=1`, `?dash=1`) injectent un CSS qui cache tous les enfants de `<body>` sauf une whitelist :

```js
'body>*:not(#examOverlay):not(#readerOverlay):not(.paywall-bg):not(#paywall):not(.paywall)...{display:none!important}'
```

**S'assurer** que `#paywall` et `.paywall` sont dans la whitelist, sinon la popup est cachée malgré `.open`.

---

## 5. Popup "Demander à Amélie" (ask-gate)

Design épuré blanc + vert + touches violet :

- Card blanche, shadow vert teal subtile
- Close X top-right
- Couronne SVG verte dans cercle vert pâle (au lieu du grand crown doré glowing)
- Tag "RÉSERVÉ AU FOCUS ILLIMITÉ" en **violet** (pill `#F5EEFE` / `#D9C3F7` / `#7c3aed`)
- Titre "Amélie IA, **ton assistante médicale**" avec accent gradient vert
- Chat preview en vert (bordures, user bubble teal, typing dots verts)
- Lock pill "FOCUS ILLIMITÉ" dans le chat en **violet**
- CTA principal "Passer au plan Focus Illimité" en **violet**

---

## 6. Funnel de fin de quiz

### Card "Bravo" (stakes + context)
- Background vert pâle (au lieu de crème doré)
- "Aux EDN, chaque place compte." avec breakdown EDN/ECOS/Parcours dans un pill violet pointillé
- "+300 places en moyenne au classement **grâce à la mémorisation répétée**"
- Highlight "+300 places" en violet (rgba 0.3 surligneur)

### Score card
- Badge année ECN/EDN en vert teal
- Headline ("Tu progresses." / "Excellent travail." / "On remet ça ?") en **violet**
- Bignum (40%, 70%, etc.) en vert teal (rouge pour <40%)
- Background : vert pâle (mid), vert (good), rouge pâle (low)

### Animation "Amélie réfléchit"
Avatar avec halo violet pulsant, dots verts, progress bar gradient violet → violet foncé. Remplace la version gold glow précédente.

### Bilan Amélie (anonyme)
- Génération locale intelligente selon le score (pct ≥ 70 / 40 / <40)
- Split du contenu :
  - **Visible** : intro + "Tes points forts : ..." + **"À retravailler en priorité :"** (teaser)
  - **Floutée** (`filter: blur(4px)` + mask fade) : noms des thèmes faibles + recommandations + "Continue avec d'autres années..."
- Limite la zone floutée à exactement 2 lignes (`max-height: calc(1.65em * 2)`)

### Card CTA fin de quiz
Remplace l'ancien simple "Voir ton bilan complet →" par une card structurée :

```
┌─────────────────────────────────────┐
│   Ton bilan personnalisé t'attend    │  ← accent vert sur "t'attend"
│                                      │
│  Tu viens de découvrir la méthode    │
│  Amélie. Passe à l'étape suivante…   │
│                                      │
│  ✓  Ton bilan détaillé : thèmes…    │  ← pills vert + checks violet
│  ✓  Tes corrigés complets : Amélie… │
│  ✓  Toutes les années : 10 ans…     │
│  ✓  +300 places en moyenne…         │
│                                      │
│  [ Passer à Focus Illimité → ]       │  ← bouton violet full-width
│                                      │
│  Ton internat se joue maintenant     │  ← hint discret
└─────────────────────────────────────┘
```

Click → `openPaywall('bilan-locked')` (au lieu de signup gate, ça pousse direct Focus Illimité).

---

## 7. Signup gate (firstQGate)

Nouveau titre/sub quand le mode est `signup` :

- Titre : **"Tu veux qu'Amélie enregistre ton résultat ?"**
- Sous-titre : "Retrouve tes résultats et continue sur d'autres dossiers."
- Bouton : **"Suivant →"** (au lieu de "Créer mon compte")
- Mode `login` inchangé ("Se connecter")

⚠️ Important : on parle de **résultat** (gratuit), pas de **corrigé** (réservé à Focus Illimité). Ne pas confondre dans les gates.

---

## 8. Paywall étendu sur tous les CTA Focus Illimité

**Tous** les CTA qui menaient direct à Stripe ou au signup gate doivent maintenant ouvrir la popup paywall refondue (contextualisée). Liste :

- Clic année verrouillée (2017-2025) dans homepage → `openPaywall('year-2017')` etc.
- Clic année verrouillée dans le year switcher (dashboard) → `openPaywall('year-XXXX')`
- Clic dossier verrouillé 2016 (autre que le free) → `openPaywall('exam-locked')`
- Clic exam verrouillé (DCP 2+, QI, LCA) → `openPaywall('exam-locked')`
- Clic "voir plus" dans correction → `openPaywall('explanation-locked')`
- Status pill dashboard "Compte gratuit · ECN 2016" → cliquable → `openPaywall('nav')`
- Bouton ghost "Passer au plan Focus Illimité : 29,90 €/mois" (dashboard) → `openPaywall('nav')`
- CTA fin de quiz "Passer à Focus Illimité pour voir ton bilan personnalisé" → `openPaywall('bilan-locked')`

### Exception (accès direct à Stripe conservé)
- Bouton Stripe dans la popup paywall elle-même (le user a déjà vu le pitch)
- CTA Stripe dans la section pricing de la homepage (`/buy.stripe.com/...`)

---

## 9. Homepage landing (`index.html`)

### Hero
- **Pas d'eyebrow** (la phrase "Plateforme n°1 EDN · Par des médecins, pour les externes" a été retirée)
- H1 : "Teste un dossier _gratuitement_. Vois la différence." (serif, `<em>` sur gratuitement)
- Sub : "Toutes les annales EDN/ECN depuis 2016 en conditions réelles. Raisonnement clinique détaillé par des médecins spécialistes : pas juste la bonne lettre."
- CTA unique : "Commencer le dossier gratuit →" (vert teal)

### Bandeau top
- Court, 1 ligne, avec enjeu au lieu d'offre produit :
- **"🎯 Mieux classé = plus de choix sur ta ville et ta spé"**

### Stats row
- 10 années / 850+ dossiers / 13 spécialités / 100% corrigé médecins

### Social proof
- "Rejoint par +5 000 D4 cette année · ⭐ 4.9/5 sur 400+ avis"
- 4 avatars ronds empilés avec **vraies faces** (jeunes adultes ~22-25) via `pravatar.cc/80?img=5/15/23/33`. URLs stables.

### Section "Comment ça marche"
**Sous-titre** ajouté : "En 3 étapes, découvre ton niveau réel, comprends ce qui te fait gagner des places, et vois l'avantage que ça peut t'apporter pour l'internat."

**3 étapes orientées bénéfice** (pas produit) :

1. **Teste un vrai dossier.** — Découvre ton niveau sur un cas concret, dans des conditions proches du concours.
2. **Vois ce que ça t'apporte.** — Compare ta copie avec la correction d'un spécialiste pour comprendre où tu peux gagner des points et des places.
3. **Transforme ça en choix.** — Plus tu progresses, plus tu augmentes tes chances de garder de l'option au moment de choisir ton internat et ta spécialité.

---

## 10. Bugs corrigés (à vérifier / reporter côté prod)

| Bug | Cause | Fix |
|---|---|---|
| Clamp 2 lignes ne marchait pas sur explication verrouillée | Règle CSS non `!important` | Forcer avec `!important` + `-webkit-line-clamp` + `display:-webkit-box` |
| `<strong>`/`<em>` sautaient sur des lignes différentes dans les pills paywall | `<li>` en `display:flex` → chaque enfant est un flex item | Wrapper le contenu dans un `<span>` unique |
| Clic sur année verrouillée sans effet (parfois) | `.tip` hover en `position:absolute;inset:0` interceptait le clic | Ajouter `pointer-events:none` sur `.tip` |
| Popup "voir plus" ne s'ouvrait pas en mode `?auto=1` | CSS early-hide avec whitelist ne contenait pas `#paywall` | Ajouter `:not(#paywall):not(.paywall)` à la whitelist |
| Badge doré "👑 ILLIMITÉ" cassait le parsing CSS en SVG data URI | Quotes mal encodées | Percent-encoder entièrement (`%20`, `%22`, `%3C`, `%3E`, `%2F`) |

---

## 11. Outil dev optionnel

**Shortcut `?endquiz=1`** : injecte des résultats fictifs (4/10, Cardiologie faible) et appelle `renderScore()` directement. Permet de tester le funnel de fin de quiz sans faire tout le quiz à chaque itération. À reproduire côté prod si utile pour les devs / QA.

```
https://<domaine>/ecn-focus.html?endquiz=1
```

---

## 📦 Historique détaillé

43 commits groupés chronologiquement dans `CHANGELOG-2026-04-22.md`. Diff complet disponible sur GitHub : `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`.

Pour reproduire les changements sur la prod, partir des commits les plus récents (rename Focus Illimité, refonte paywall, neuromarketing fin de quiz, rebuild homepage). Les commits intermédiaires (itérations sur les couleurs, ajustements) peuvent être ignorés.
