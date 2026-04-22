# Guide de migration vers la prod — 22 avril 2026

Document à transmettre au dev de la vraie codebase pour porter les changements faits sur la maquette statique `ASKAMELIE-TEST-MAQUETTE` vers la version prod dynamique.

- **Maquette source** : https://ecn.askamelie.com (statique, 2 fichiers HTML)
- **Repo** : GitHub `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`
- **Fichiers touchés** : `index.html`, `ecn-focus.html`
- **43 commits** sur `main` du 2026-04-22 (voir `CHANGELOG-2026-04-22.md` pour l'historique)

---

## 📋 Table des matières

1. [Règles d'ouverture de popups — matrice des CTA](#regles)
2. [Visuels ASCII des popups principaux](#visuels)
3. [Rebranding Premium → Focus Illimité](#rebranding)
4. [Nettoyage des em-dashes](#emdashes)
5. [Palette couleur harmonisée](#palette)
6. [Popup paywall (modal verrouillage)](#paywall)
7. [Popup "Demander à Amélie" (ask-gate)](#askgate)
8. [Funnel de fin de quiz](#finquiz)
9. [Signup gate (firstQGate)](#signup)
10. [Homepage landing](#homepage)
11. [Bugs corrigés à vérifier côté prod](#bugs)
12. [Outil dev optionnel](#devtool)

---

<a id="regles"></a>
## 1. ⚡ Règles d'ouverture de popups — matrice des CTA

**Principe général** : tous les CTA Focus Illimité ouvrent la popup paywall (contextualisée) AVANT d'aller à Stripe. Le user doit voir le pitch avant de payer.

### Popup paywall à ouvrir (contexte → titre)

| Action du user | Popup ? | Contexte | Titre affiché |
|---|---|---|---|
| Clic sur année verrouillée 2017-2022 (homepage) | ✅ | `year-2017` etc. | `ECN 2017` |
| Clic sur année verrouillée 2023-2025 (homepage) | ✅ | `year-2023` etc. | `EDN 2023` |
| Clic sur année verrouillée dans year switcher (dashboard) | ✅ | `year-XXXX` | `ECN/EDN XXXX` |
| Clic sur dossier 2 à N de 2016 (2016 free, 1 dossier gratuit only) | ✅ | `exam-locked` | 🔒 Cette épreuve est réservée |
| Clic sur exam verrouillé (DCP 2+, QI, LCA hors 2016) | ✅ | `exam-locked` | 🔒 Cette épreuve est réservée |
| Clic sur "voir plus" dans correction verrouillée | ✅ | `explanation-locked` | 🔒 Correction complète réservée |
| Clic sur body de correction verrouillée (clamp 2 lignes) | ✅ | `explanation-locked` | 🔒 Correction complète réservée |
| Clic sur status pill "Compte gratuit · ECN 2016" (dashboard) | ✅ | `nav` | 🔒 Accès Focus Illimité |
| Clic sur bouton ghost "Passer au plan Focus Illimité" (dashboard) | ✅ | `nav` | 🔒 Accès Focus Illimité |
| Clic sur CTA fin de quiz "Passer à Focus Illimité pour voir ton bilan perso" | ✅ | `bilan-locked` | 🔒 Ton bilan personnalisé |
| Clic sur bouton nav "Focus Illimité" (topbar) | ✅ | `nav` | 🔒 Accès Focus Illimité |
| Post-signup (après création compte) | ✅ | `post-signup` | 🎉 Bienvenue chez Ask Amélie |

### Popup ask-gate (Demander à Amélie) à ouvrir

| Action du user | Popup ? |
|---|---|
| Clic sur bouton "Demander à Amélie" dans feedback quiz | ✅ Ask-gate (pas paywall) |

### Popup signup gate (firstQGate) à ouvrir

| Action du user | Popup ? | Mode |
|---|---|---|
| Anon tente un 2e dossier 2016 (alors qu'un autre a déjà été utilisé) | ❌ → on ouvre la **paywall** maintenant (was signup avant) | — |
| Clic "Créer mon compte →" sur une section dédiée | ✅ | `signup` |
| Clic "Se connecter" (lien bas de paywall) | ✅ | `login` |
| Clic "goPremium()" pour user non authed | ✅ (1er étape du funnel signup → Stripe) | `signup` avec `premiumIntent=true` |

### Accès direct à Stripe (sans popup) — **exceptions**

| Action | Stripe direct ? |
|---|---|
| Bouton Stripe DANS la popup paywall | ✅ Direct Stripe (déjà pitch vu) |
| CTA pricing section homepage ("Démarrer Focus Illimité") | ✅ Direct Stripe (section dédiée) |
| CTA footer "Devenir Focus Illimité - 29,90 €/mois" | ✅ Direct Stripe |
| Sticky bar bottom "Passer Focus Illimité" | ✅ Direct Stripe |

---

<a id="visuels"></a>
## 2. 🎨 Visuels ASCII des popups principaux

### 🔒 Popup paywall année verrouillée (cas "ECN 2018")

```
┌─────────────────────────────────────────┐
│                              [ × ]       │  ← close en haut droite
│                                          │
│           ╭─────╮                        │
│           │ 🔒  │  ← icône cadenas SVG   │
│           ╰─────╯     dans cercle vert   │
│                                          │
│              ECN 2018                    │  ← titre dynamique
│                                          │
│       Accessible uniquement via          │  ← sub
│          Focus Illimité.                 │
│                                          │
│  ┌──────────────────────────────────┐   │
│  │ ✓  Toutes les années d'annales    │   │  ← pill vert clair
│  │    (2016-2025)                    │   │     checkmark violet
│  └──────────────────────────────────┘   │
│  ┌──────────────────────────────────┐   │
│  │ ✓  Tes corrigés complets : Amélie │   │
│  │    t'explique chaque question     │   │
│  └──────────────────────────────────┘   │
│  ┌──────────────────────────────────┐   │
│  │ ✓  Ton bilan personnalisé détaillé│   │
│  │    après chaque dossier           │   │
│  └──────────────────────────────────┘   │
│  ┌──────────────────────────────────┐   │
│  │ ✓  +300 places en moyenne au      │   │
│  │    classement pour nos élèves     │   │
│  └──────────────────────────────────┘   │
│                                          │
│  ┌──────────────────────────────────┐   │
│  │   Passer à Focus Illimité →      │   │  ← bouton violet full-width
│  └──────────────────────────────────┘   │
│                                          │
│       Sans engagement · Moins d'1 €/jour │
│  ─────────────────────────────────────  │
│   Tu as déjà souscrit ? Se connecter →   │
└─────────────────────────────────────────┘
```

### 👑 Popup "Demander à Amélie" (ask-gate)

```
┌─────────────────────────────────────────┐
│                              [ × ]       │
│           ╭─────╮                        │
│           │ 👑  │  ← couronne SVG verte  │
│           ╰─────╯                        │
│                                          │
│    ⟪ RÉSERVÉ AU FOCUS ILLIMITÉ ⟫         │  ← tag violet
│                                          │
│  Amélie IA, ton assistante médicale      │  ← "ton assistante…" en vert
│                                          │
│  Accède à une assistante IA avec         │
│  +10 millions de connaissances           │
│  médicales à portée de question.         │
│                                          │
│  Avec Focus Illimité, ton parcours       │
│  devient ultra-personnalisé : Amélie     │
│  repère tes faiblesses, adapte tes       │
│  prochains dossiers…                     │
│                                          │
│  ┌────────────────────────────────────┐ │
│  │ ● AMÉLIE · EN DIRECT               │ │  ← chat preview vert
│  │  ┌──────────────────────────┐      │ │
│  │  │ Pourquoi la réponse B…   │ Toi  │ │  ← user bubble vert teal
│  │  └──────────────────────────┘      │ │
│  │  [A] Amélie · Classique du dossier │ │  ← bot bubble blanc
│  │      cardio…                       │ │
│  │  [A] ••• (typing dots verts)       │ │
│  │                  [ FOCUS ILLIMITÉ ] │ │  ← lock pill violet
│  └────────────────────────────────────┘ │
│                                          │
│  ┌──────────────────────────────────┐   │
│  │  Passer au plan Focus Illimité → │   │  ← bouton violet
│  └──────────────────────────────────┘   │
│                                          │
│              Plus tard                   │
└─────────────────────────────────────────┘
```

### 🎯 Card CTA fin de quiz

```
┌─────────────────────────────────────────┐
│                                          │
│   Ton bilan personnalisé t'attend        │  ← titre, "t'attend" vert gradient
│                                          │
│   Tu viens de découvrir la méthode       │
│   Amélie. Passe à l'étape suivante :     │
│   le plan de travail qui va vraiment     │
│   te faire gagner des places.            │
│                                          │
│  ┌──────────────────────────────────┐   │
│  │ ✓  Ton bilan détaillé : thèmes    │   │
│  │    faibles et plan de travail…    │   │
│  └──────────────────────────────────┘   │
│  ┌──────────────────────────────────┐   │
│  │ ✓  Tes corrigés complets : Amélie │   │
│  │    t'explique chaque question…    │   │
│  └──────────────────────────────────┘   │
│  ┌──────────────────────────────────┐   │
│  │ ✓  Toutes les années : 10 ans     │   │
│  │    d'annales, 850+ dossiers…      │   │
│  └──────────────────────────────────┘   │
│  ┌──────────────────────────────────┐   │
│  │ ✓  +300 places en moyenne au      │   │
│  │    classement pour nos élèves     │   │
│  └──────────────────────────────────┘   │
│                                          │
│  ┌──────────────────────────────────┐   │
│  │    Passer à Focus Illimité →      │  ← bouton violet
│  └──────────────────────────────────┘   │
│                                          │
│  Ton internat se joue maintenant ·       │
│  sans engagement                         │
└─────────────────────────────────────────┘
```

### 📊 Bilan Amélie avec teaser (anonyme, fin de quiz)

```
┌─────────────────────────────────────────┐
│  [A] Analyse d'Amélie                    │
│      À l'instant                         │
│  ─────────────────────────────────────   │
│                                          │
│  Premier passage qui pose les jalons.    │  ← visible, net
│  Pas de panique. C'est exactement le     │
│  genre de dossier qui te fait progresser │
│  le plus.                                │
│                                          │
│  Tes points forts : Neurologie.          │  ← visible, net
│                                          │
│  À retravailler en priorité :            │  ← visible, net (TEASER)
│                                          │
│  ╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳  ← floutée
│  ╳╳ blur 4px + mask fade ╳╳  (2 lignes  ← blur 4px
│                                max)      │
│                                          │
│  Tu n'as accès qu'à un aperçu du         │
│  bilan d'Amélie.                         │
│                                          │
│  [ Passer à Focus Illimité pour voir    │
│    ton bilan personnalisé → ]            │
└─────────────────────────────────────────┘
```

### 🎓 Homepage hero

```
 🎯 Mieux classé = plus de choix sur ta ville et ta spé    ← bandeau top vert
────────────────────────────────────────────────────────
 [ Askamélie ]              Annales  Gratuit vs Focus    [ Connexion ]
────────────────────────────────────────────────────────

                    Teste un dossier
                    gratuitement.                ← H1 serif
                    Vois la différence.

            Toutes les annales EDN/ECN depuis 2016 en
         conditions réelles. Raisonnement clinique détaillé
              par des médecins spécialistes : pas juste
                          la bonne lettre.

                ┌──────────────────────────────┐
                │ Commencer le dossier gratuit →│   ← CTA vert
                └──────────────────────────────┘

           10         850+          13           100%
         ANNÉES    DOSSIERS    SPÉCIALITÉS   CORRIGÉ MÉDECINS

     (👤)(👤)(👤)(👤)  Rejoint par +5 000 D4 cette année
                           · ⭐ 4.9/5 sur 400+ avis
```

---

<a id="rebranding"></a>
## 3. Rebranding complet `Premium` → `Focus Illimité`

### À faire en prod
Rechercher/remplacer sur tous les textes user-facing :
- `Premium` → `Focus Illimité`
- `l'Illimité` → `le Focus Illimité`
- `à l'Illimité` → `au Focus Illimité`

### Grammaire à surveiller
Après rename, vérifier `à le` / `de le` : doivent devenir `au` / `du`.

### ⚠️ Ne pas toucher
- Identifiants techniques : `_IS_PREMIUM`, `goPremium()`, `trackPremiumClick()`, `premium-active` (CSS class).
- Les variables/flags internes gardent leur nom.

---

<a id="emdashes"></a>
## 4. Nettoyage des em-dashes `—`

Les em-dashes dans le texte user-facing sonnent "IA généré". Remplacer :
- `"2016 — 2025"` → `"2016-2025"`
- `" — "` dans les descriptions → `" : "` ou `". "`

Garder les `—` uniquement dans commentaires code + placeholders "pas de valeur".

---

<a id="palette"></a>
## 5. Palette couleur harmonisée

| Usage | Couleur | Hex |
|---|---|---|
| Branding général, info, borders | **Vert teal** | `#0E5552` / `#1B6E68` |
| Backgrounds pâles, pills info | **Vert sage** | `#EAF0EA` / `#C4D3C5` / `#F5F9F5` |
| Action premium (CTA, checkmarks, badges verrouillage) | **Violet** | `#A855F7` / `#7c3aed` |
| Backgrounds violet pâle (tag premium) | **Lavande** | `#F5EEFE` / `#D9C3F7` |
| Highlights (ex: surligneur "+300 places") | **Violet translucide** | `rgba(168,85,247,.3)` |

### ❌ Supprimer partout le doré/gold
Anciennes classes avec `#F5C542`, `#D4A017`, `#F59E0B`, `#D97706`, `#5B3F00` → remplacer :
- CTA / lien premium → **violet**
- Décoration / badge verrouillage → **violet**
- Stats neutres (warning bar orange dashboard) → OK à garder

### Couronne 👑 → SVG vectoriel

```html
<svg viewBox="0 0 24 24" fill="currentColor">
  <path d="M3 17h18l-1.5-9L15 12 12 5 9 12 4.5 8 3 17zm0 2h18v2H3v-2z"/>
</svg>
```

Inlined en data URI `background-image` sur pseudo-éléments `::after`/`::before`. **Tout percent-encoder** (`%20`, `%22`, `%3C`, `%3E`, `%2F`) pour éviter bugs de parsing CSS.

---

<a id="paywall"></a>
## 6. Popup paywall (modal verrouillage)

### Structure HTML simplifiée

```html
<div class="paywall" id="paywall">
  <div class="paywall-box">
    <button class="pw-close">×</button>
    <div class="pw-lock"><svg>cadenas vert</svg></div>
    <div class="pw-card">
      <h3 id="pwTitle">ECN 2018</h3>
      <p class="pw-sub" id="pwSub">
        Accessible uniquement via <strong>Focus Illimité</strong>.
      </p>
      <ul class="pw-feats">
        <li><span><strong>Toutes les années</strong> d'annales (2016-2025)</span></li>
        <li><span><strong>Tes corrigés complets</strong> : Amélie t'explique chaque question</span></li>
        <li><span><strong>Ton bilan personnalisé</strong> détaillé après chaque dossier</span></li>
        <li><span><strong>+300 places en moyenne</strong> au classement pour nos élèves</span></li>
      </ul>
      <a class="pw-cta-gold" href="https://buy.stripe.com/...">Passer à Focus Illimité →</a>
      <button class="pw-learn-more">Plus tard</button>
    </div>
  </div>
</div>
```

> **Important** : wrapper le contenu des `<li>` dans un `<span>` pour éviter que `<strong>`/`<em>` deviennent des flex items séparés.

### CSS clé

```css
.paywall{position:fixed;inset:0;z-index:10800;background:rgba(15,23,42,.72);
  backdrop-filter:blur(10px);display:none;
  align-items:center;justify-content:center;padding:24px}
.paywall.open{display:flex;animation:fIn .25s ease}
.paywall-box{background:#fff;border-radius:22px;padding:32px 26px 20px;
  max-width:460px;width:100%;box-shadow:0 30px 80px rgba(14,85,82,.3)}
.pw-feats li{display:flex;align-items:flex-start;gap:10px;padding:10px 13px;
  background:#EAF0EA;border:1px solid #C4D3C5;border-radius:11px;font-size:13px}
.pw-feats li::before{content:'✓';width:20px;height:20px;border-radius:50%;
  background:linear-gradient(135deg,#A855F7 0%,#7c3aed 100%);color:#fff}
.pw-cta-gold{background:linear-gradient(135deg,#A855F7 0%,#7c3aed 100%);
  color:#fff;border-radius:14px;padding:15px 22px;width:100%;
  box-shadow:0 10px 28px rgba(168,85,247,.42)}
```

### ⚠️ Piège : whitelist early-hide CSS

Certains modes (`?auto=1`, `?dash=1`) injectent ce CSS :

```js
'body>*:not(#examOverlay):not(#readerOverlay):not(.paywall-bg):not(#paywall):not(.paywall)...{display:none!important}'
```

**Vérifier que `#paywall` et `.paywall` sont bien dans la whitelist**, sinon la popup est cachée.

---

<a id="askgate"></a>
## 7. Popup "Demander à Amélie" (ask-gate)

Design épuré blanc + vert + touches violet :

- Card blanche, shadow vert teal subtile
- Close X top-right (`#EDEBE7` bg, hover `#E3DFD8`)
- Couronne SVG verte dans cercle vert pâle
- Tag "RÉSERVÉ AU FOCUS ILLIMITÉ" en **violet** (pill `#F5EEFE` / `#D9C3F7` / `#7c3aed`)
- Titre "Amélie IA, **ton assistante médicale**" avec accent gradient vert
- Chat preview en vert (bordures, user bubble `#0E5552`, typing dots `#A855F7` → wait, let me recheck — typing dots verts `#0E5552`)
- Lock pill "FOCUS ILLIMITÉ" dans le chat en **violet**
- CTA principal "Passer au plan Focus Illimité" en **violet**
- Ghost "Plus tard" gris

---

<a id="finquiz"></a>
## 8. Funnel de fin de quiz

### Card "Bravo" (stakes)
- Background vert pâle (au lieu de crème doré)
- "Aux EDN, chaque place compte."
- Breakdown `EDN 60% · ECOS 30% · Parcours 10%` dans pill avec **contour pointillé violet 2px** (`border: 2px dashed #A855F7`)
- "+300 places en moyenne au classement **grâce à la mémorisation répétée**"
- Highlight "+300 places" en violet surligneur (rgba 0.3)

### Score card
- Badge année ECN/EDN en vert teal
- Headline ("Tu progresses.", "Excellent travail.", "On remet ça ?") en **violet** `#7c3aed`
- Bignum (40%, 70%, etc.) en vert teal (rouge pour <40%)
- Background :
  - `mid` (40-69%) → vert pâle `#F5F9F5` + bordure `#C4D3C5`
  - `good` (≥70%) → vert plus soutenu `#EAF0EA`
  - `low` (<40%) → rouge pâle

### Animation "Amélie réfléchit"
- Avatar avec halo violet pulsant (`rgba(168,85,247,.45)` → pulse)
- Dots verts `#A855F7`
- Progress bar gradient violet `#A855F7 → #7c3aed`
- Radial glow violet dans le card

### Bilan Amélie (anonyme)
- Génération locale intelligente selon le score (pct ≥ 70 / 40 / <40)
- Split du contenu :
  - **Visible** : intro + "Tes points forts : ..." + "**À retravailler en priorité :**" (teaser)
  - **Floutée** : noms thèmes faibles + "Refais 1-2 dossiers…" + "Continue avec d'autres années…"
- CSS zone floutée :

```css
.report-content-hidden{
  max-height:calc(1.65em * 2);  /* 2 lignes exactement */
  overflow:hidden;
  filter:blur(4px);
  user-select:none;
  pointer-events:none;
  mask-image:linear-gradient(to bottom,#000 0%,#000 60%,transparent 100%);
}
```

### Card CTA fin de quiz → `openPaywall('bilan-locked')`

(Voir visuel ASCII section 2.)

---

<a id="signup"></a>
## 9. Signup gate (firstQGate)

Nouveau titre/sub mode `signup` :

| Champ | Valeur |
|---|---|
| Titre | **"Tu veux qu'Amélie enregistre ton résultat ?"** |
| Sous-titre | "Retrouve tes résultats et continue sur d'autres dossiers." |
| Bouton | **"Suivant →"** (au lieu de "Créer mon compte") |

Mode `login` inchangé ("Se connecter").

> ⚠️ **Important** : on parle de **résultat** (gratuit), pas de **corrigé** (réservé Focus Illimité).

---

<a id="homepage"></a>
## 10. Homepage landing (`index.html`)

### Hero
- **Pas d'eyebrow** (ex-"Plateforme n°1 EDN" retirée)
- H1 : `Teste un dossier <em>gratuitement</em>.<br>Vois la différence.` (serif, italique accent vert sur "gratuitement")
- Sub : "Toutes les annales EDN/ECN depuis 2016 en conditions réelles. Raisonnement clinique détaillé par des médecins spécialistes : pas juste la bonne lettre."
- CTA unique : "Commencer le dossier gratuit →" (vert teal)

### Bandeau top (1 ligne)
**"🎯 Mieux classé = plus de choix sur ta ville et ta spé"**

### Stats row
- 10 années / 850+ dossiers / 13 spécialités / 100% corrigé médecins

### Social proof
- Texte : "Rejoint par +5 000 D4 cette année · ⭐ 4.9/5 sur 400+ avis"
- 4 avatars ronds empilés avec **vraies faces** jeunes adultes via `pravatar.cc/80?img=5/15/23/33`

### Section "Comment ça marche"
Sous-titre : "En 3 étapes, découvre ton niveau réel, comprends ce qui te fait gagner des places, et vois l'avantage que ça peut t'apporter pour l'internat."

3 étapes orientées **bénéfice** (pas produit) :

| # | Titre | Description |
|---|---|---|
| 1 | **Teste un vrai dossier.** | Découvre ton niveau sur un cas concret, dans des conditions proches du concours. |
| 2 | **Vois ce que ça t'apporte.** | Compare ta copie avec la correction d'un spécialiste pour comprendre où tu peux gagner des points et des places. |
| 3 | **Transforme ça en choix.** | Plus tu progresses, plus tu augmentes tes chances de garder de l'option au moment de choisir ton internat et ta spécialité. |

---

<a id="bugs"></a>
## 11. Bugs corrigés à vérifier côté prod

| Bug | Cause | Fix |
|---|---|---|
| Clamp 2 lignes KO sur explication verrouillée | Règle CSS non `!important` | `!important` + `-webkit-line-clamp:2` + `display:-webkit-box` |
| `<strong>`/`<em>` sautent sur lignes différentes | `<li>` en flex → chaque enfant = flex item | Wrapper contenu dans `<span>` |
| Clic année verrouillée sans effet (parfois) | `.tip` hover `position:absolute;inset:0` bloquait | `pointer-events:none` sur `.tip` |
| Popup voir plus KO en `?auto=1` | Whitelist early-hide ne contenait pas `#paywall` | Ajouter `:not(#paywall):not(.paywall)` |
| SVG data URI casse parsing CSS | Quotes mal encodées | Percent-encoder tout (`%20`, `%22`, `%3C`, `%3E`, `%2F`) |

---

<a id="devtool"></a>
## 12. Outil dev optionnel

**Shortcut `?endquiz=1`** : injecte des résultats fictifs (4/10, Cardiologie faible) et appelle `renderScore()` directement. Permet de tester le funnel de fin sans refaire tout le quiz.

```
https://<domaine>/ecn-focus.html?endquiz=1
```

À reproduire côté prod si utile pour QA.

---

## 📦 Fichiers à examiner dans le repo

```
ASKAMELIE-TEST-MAQUETTE/
├── index.html                    → homepage (hero, stats, "Comment ça marche", paywall)
├── ecn-focus.html                → quiz engine (explanation, ask-gate, score card, year switcher, paywall)
├── CHANGELOG-2026-04-22.md       → historique chronologique des 43 commits
└── MIGRATION-PROD.md             → ce fichier (guide de migration)
```

GitHub : `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`, dernier commit sur la journée du 2026-04-22.
