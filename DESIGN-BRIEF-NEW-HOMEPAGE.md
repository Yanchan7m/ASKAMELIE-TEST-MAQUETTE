# Design brief — Nouvelle homepage Ask Amélie

Inspiration : Docgenda (health-connect-whitelistadssol.replit.app/edn) — "Le logiciel tout-en-un des professionnels de santé".
Objectif : **refresh** de la homepage actuelle en gardant le logo Ask Amélie existant, tout en modernisant le design pour ancrer la plateforme comme **l'outil tout-en-un des externes en médecine qui préparent l'EDN**.

- **Logo actuel** à conserver : texte "Ask Amélie" (Libre Baskerville serif) + accent coloré (dot vert teal)
- **Avatar Amélie** : `amelie-256.jpg` (photo ronde, médecin souriante)
- **Fichiers cibles** : `index.html`

---

## 🎯 1. Positionnement & angle

### Message clé (reprendre la logique Docgenda)
> **"Ask Amélie — Le compagnon tout-en-un des externes pour décrocher l'internat que tu veux."**

**Pourquoi ce pivot ?**
- Docgenda cible les pros de santé avec un "tout-en-un". Ask Amélie peut reprendre la même mécanique côté **externes** : entraînement + corrections + bilan + tutorat humain dans UNE plateforme.
- Le positionnement "tout-en-un" structure naturellement les sections de la homepage.

---

## 🎨 2. Identité visuelle — à garder

### Logo (inchangé)
```html
<a href="index.html" class="logo">
  <span class="dot"></span>Ask Amélie
</a>
```

CSS :
```css
.logo{font-family:'Libre Baskerville',Georgia,serif;font-size:18px;font-weight:700;letter-spacing:-.015em;color:#0E5552}
.logo .dot{width:9px;height:9px;border-radius:50%;background:#00B85E;display:inline-block;margin-right:6px}
```

### Avatar Amélie
- `amelie-256.jpg` dans le hero (large, ronde, border blanc + glow vert flash)
- `amelie-128.jpg` en plus petit ailleurs (chat preview, bilan, CTA inline)

### Palette (Clinical Green — déjà établie)
- **Vert principal teal** `#0E5552` — branding, headings, borders
- **Vert clair menthe** `#E8F4F1` / `#EAF0EA` — backgrounds neutres, pills, cards alternées
- **Vert flash** `#00E676` / `#00B85E` — CTA uniquement, checkmarks, badges premium

### Typographie
- **Serif** : `Libre Baskerville` Georgia (titres, grands nombres, logo)
- **Sans-serif** : `Work Sans`, Inter, `-apple-system` (corps, UI)

---

## 🧱 3. Structure de la nouvelle homepage

### Section 1 — Bandeau top (déjà en place)
> 🎯 **Mieux classé** = plus de choix sur ta ville et ta spé

Plein vert teal, texte blanc, 1 ligne, fixe en haut.

### Section 2 — Header/Nav
```
[● Ask Amélie]         Annales | Gratuit vs Focus Illimité       [Connexion]
```

- Logo à gauche (dot vert flash + nom)
- 2 liens nav centraux (ancres)
- Bouton "Connexion" dark teal à droite

### Section 3 — Hero (NOUVEAU — style Docgenda)

**Layout** : 2 colonnes sur desktop, 1 colonne stackée mobile.

**Colonne gauche (texte + CTA)** :
- Pill eyebrow (optionnel) : `🎓 Externes D4 · Préparation EDN`
- H1 serif gros (48-56px) :
  > Teste un dossier _gratuitement_.<br>Vois la différence.
- Sous-titre (16px, 60% width) :
  > Toutes les annales EDN/ECN depuis 2016, corrigées en profondeur par des médecins spécialistes. Pas juste la bonne lettre — le raisonnement qui te fait gagner des places.
- CTA primaire vert teal : **"Commencer le dossier gratuit →"**
- Lien secondaire underline : "Comment ça marche ↓" (ancre vers section 5)
- Social proof sous CTA :
  `(👤)(👤)(👤)(👤)` Rejoint par **+5 000 D4** cette année · ⭐ **4.9/5** sur 400+ avis

**Colonne droite (visuel)** :
- **Mockup du quiz** : faux screenshot de l'écran de correction d'Amélie (iframe-style card, floating, ombre portée)
- Ou : **avatar Amélie** en grand (256px ronde, border blanc 4px, glow vert flash subtil derrière via radial-gradient)
- Badge flottant au-dessus : "🤖 IA médicale certifiée par Amélie" ou "Disponible 24/7"

### Section 4 — Barre de stats (existante, resserrée)

```
   10              850+             13              100%
 ANNÉES          DOSSIERS       SPÉCIALITÉS    CORRIGÉ MÉDECINS
```

4 colonnes sur desktop, 2×2 grid mobile. Grand chiffre serif vert teal, label uppercase gris sage.

### Section 5 — "Comment ça marche" (refonte Docgenda-style)

Sous-titre ajouté :
> **En 3 étapes**, découvre ton niveau réel, comprends ce qui te fait gagner des places, et vois l'avantage que ça peut t'apporter pour l'internat.

3 cards parfaitement égales (largeur + hauteur), numérotées 1/2/3.

| # | Titre | Description |
|---|---|---|
| 1 | **Teste un vrai dossier.** | Découvre ton niveau sur un cas concret, dans des conditions proches du concours. |
| 2 | **Vois ce que ça t'apporte.** | Compare ta copie avec la correction d'un spécialiste pour comprendre où tu peux gagner des points et des places. |
| 3 | **Transforme ça en choix.** | Plus tu progresses, plus tu augmentes tes chances de garder de l'option au moment de choisir ton internat et ta spécialité. |

Card 1 a un CTA "Commencer →" vert flash. Cards 2-3 sans CTA (lecture seule).

### Section 6 — "Tout-en-un" (NOUVEAU — inspiré Docgenda)

**Titre** :
> Tout ce qu'il te faut, dans **un seul outil**.

**Sous-titre** :
> Plus besoin de jongler entre 5 plateformes. Ask Amélie regroupe annales, corrigés détaillés, suivi personnel et tutorat humain.

**Grid de 4 features** (2×2 ou 4 colonnes) :

| Feature | Icône | Description |
|---|---|---|
| **850+ dossiers corrigés** | 📚 (ou SVG book) | 10 ans d'annales EDN/ECN (2016-2025), tous corrigés par des médecins. |
| **Amélie IA** | 👑 couronne | Ton assistante médicale IA : pose-lui n'importe quelle question pendant ton quiz. |
| **Bilan personnalisé** | 📊 graph | Après chaque dossier, Amélie analyse tes points forts et tes thèmes faibles. |
| **Tutorat humain** | 👥 | Session 1-on-1 avec Amélie (30 min, 11,99 €) pour débloquer un point précis. |

### Section 7 — Preuve sociale (testimonials)

3 cartes témoignages. Format :
```
┌───────────────────────┐
│ "Grâce aux corrigés…" │
│                        │
│  [avatar] Clément L.   │
│           D4, Paris 7  │
│  ⭐⭐⭐⭐⭐             │
└───────────────────────┘
```

Vraies faces via `pravatar.cc/80?img=...` (cohérent avec le social proof hero).

### Section 8 — Grille des années (existante)

10 cards années (2016 à 2025). Année 2016 = gratuite (badge vert flash). Autres = locked avec badge couronne + "ILLIMITÉ" violet → maintenant vert flash.

Click sur année verrouillée → popup paywall refondue (voir MIGRATION-PROD.md).

### Section 9 — Offres (Gratuit vs Focus Illimité)

Tableau de comparaison 2 colonnes :

| | **Gratuit** | **Focus Illimité** |
|---|---|---|
| Prix | 0 € | 29,90 € / mois |
| Années | 2016 uniquement | **2016-2025** |
| Dossiers | 1 | **850+** |
| Corrigés | Aperçu | **Complets** |
| Bilan Amélie | Teaser | **Détaillé** |
| Amélie IA | — | **Illimité** |
| Support | — | Session tutorat |

CTA "Passer à Focus Illimité →" vert flash en bas.

### Section 10 — FAQ (NOUVEAU)

4-6 questions repliables (accordion). Exemples :
- Pourquoi Ask Amélie est-il différent des autres plateformes EDN ?
- Quels médecins ont écrit les corrigés ?
- Puis-je annuler à tout moment ?
- Est-ce compatible avec ma préparation existante ?

### Section 11 — CTA final (pleine largeur)

Bloc vert teal plein largeur avec :
- Titre serif : "Tu es à 1 dossier de voir la différence."
- Sous-titre : "Ton dossier gratuit t'attend. Pas d'inscription requise."
- Gros bouton blanc "Commencer maintenant →"

### Section 12 — Footer (simple)

Logo + liens légaux :
- Mentions légales
- CGU
- Politique de confidentialité
- Contact

---

## 🧩 4. Composants réutilisables

### Bouton CTA primaire (vert teal)
```css
background: #0E5552;
color: #fff;
border-radius: 12px;
padding: 14px 28px;
font-weight: 600;
box-shadow: 0 2px 10px rgba(14,85,82,.2);
hover: transform: translateY(-1px); background: #08403E;
```

### Bouton CTA secondaire (vert flash — pour actions premium)
```css
background: linear-gradient(135deg, #00E676, #00B85E);
color: #fff;
border-radius: 14px;
padding: 15px 22px;
font-weight: 800;
box-shadow: 0 10px 28px rgba(0,230,118,.42);
```

### Card feature ("Tout-en-un")
```css
background: #fff;
border: 1px solid #C4D3C5;
border-radius: 16px;
padding: 28px 24px;
box-shadow: 0 8px 28px rgba(14,85,82,.06);

/* icon wrap */
.feat-icon {
  width: 48px; height: 48px;
  border-radius: 12px;
  background: #EAF0EA;
  color: #00B85E;
}
```

### Pill / Badge (couronne ILLIMITÉ)
```css
background: linear-gradient(135deg, #00E676, #00B85E);
color: #fff;
padding: 4px 10px 4px 24px;
border-radius: 100px;
font-size: 11px;
font-weight: 900;
/* crown SVG via background-image, 10px, positioned left */
```

---

## 📐 5. Layout & responsive

- **Container max-width** : 1100px
- **Hero** : 2 colonnes desktop (540px / 420px), stack mobile (<900px)
- **Sections principales** : 880px max-width, padding horizontal 24px
- **Cards "Comment ça marche"** : 3 colonnes desktop, 1 colonne mobile (<720px)
- **Grille années** : 5×2 desktop, 3×4 tablet (<900px), 2×5 mobile (<600px)
- **Features "Tout-en-un"** : 4 colonnes desktop, 2×2 mobile

Breakpoints :
- Desktop : 1100px+
- Tablet : 720-1099px
- Mobile : <720px
- Small mobile : <480px (ajustements padding réduits)

---

## ✨ 6. Micro-interactions & animations

### Au load
- Fade + slide-up sur le hero text (300ms cubic-bezier)
- Fade in de l'avatar Amélie avec glow pulsant (subtil, 3s loop)

### Hover
- Cards "Comment ça marche" : translateY(-2px) + shadow plus forte
- CTA primaire : translateY(-1px) + shadow plus forte
- Year cards : border vert + glow subtil

### Scroll
- Stats s'animent en comptant de 0 au chiffre final (IntersectionObserver)
- Avatars du social proof apparaissent en cascade

### Au clic
- Popup paywall s'ouvre avec `egRise` cubic-bezier (vertical slide + fade)
- Les feature cards peuvent avoir un léger "ripple" vert flash au clic

---

## 🎨 7. Éléments visuels distinctifs à ajouter

### Mockup quiz (hero droite, optionnel)
Faux screenshot d'un écran de correction Amélie, affichant :
- Question type QCM
- Réponse utilisateur cochée en rouge
- Bonne réponse en vert
- Bulle "Amélie t'explique" avec texte
- Badge couronne "Focus Illimité" en coin

Placé en card floating avec ombre portée forte, légèrement tourné (3°).

### Illustrations de thèmes médicaux (section "Tout-en-un")
Petites illustrations minimalistes vert teal pour chaque feature. Style plat, line-art, 48×48px.

### Confetti / particules animées (CTA final)
Particules vert flash qui flottent doucement autour du bouton "Commencer maintenant" — très subtil, pas intrusif.

---

## 📄 8. Copy / messages à réutiliser

### Principales (de la maquette actuelle)
- "Mieux classé = plus de choix sur ta ville et ta spé" (bandeau)
- "Teste un dossier gratuitement. Vois la différence." (H1)
- "+300 places en moyenne au classement grâce à la mémorisation répétée" (preuve)
- "Ton internat se joue maintenant · sans engagement" (rassurance)

### Nouveaux (inspirés Docgenda "tout-en-un")
- "Tout ce qu'il te faut, dans un seul outil."
- "Plus besoin de jongler entre 5 plateformes."
- "Le compagnon tout-en-un des externes."
- "Ton entraînement EDN, augmenté par l'IA."

---

## 🚀 9. Étapes de mise en œuvre suggérées

1. **Phase 1 — Fondations** : garder l'`index.html` actuel, ajouter les sections manquantes (Tout-en-un, FAQ, CTA final pleine largeur)
2. **Phase 2 — Hero refonte** : transformer le hero actuel en 2 colonnes avec visuel à droite (mockup ou avatar grand)
3. **Phase 3 — Polish** : animations au scroll, micro-interactions, illustrations features
4. **Phase 4 — A/B test** : comparer conversion entre l'ancien hero et le nouveau

---

## 📄 Fichiers associés

- `index.html` : homepage cible (fichier existant à refaire)
- `MIGRATION-PROD.md` : guide global palette Clinical Green + popups
- `MIGRATION-COLOR-CLINICAL-GREEN.md` : détail swap violet → vert
- **Ce document** : brief pour la refonte homepage (inspiré Docgenda)

Site inspiration : https://health-connect-whitelistadssol.replit.app/edn (Docgenda)
