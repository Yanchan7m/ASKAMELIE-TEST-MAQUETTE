# Changelog — 22 avril 2026

Journée de refonte neuromarketing et branding Focus Illimité.

## 🎨 Design & branding

### Rebranding complet
- **Premium → Illimité → Focus Illimité** : rename sur tous les textes user-facing (73+ occurrences sur `index.html` + `ecn-focus.html`)
- Grammaire ajustée : `l'Illimité` → `le Focus Illimité`, `à l'Illimité` → `au Focus Illimité`, fix `"Réservé à le Focus Illimité"` → `"Réservé au Focus Illimité"`
- Em-dashes `—` nettoyés (style "trop IA") : remplacés par `:` ou `-` selon le contexte (~110 occurrences)
- Couronne emoji 👑 → **SVG vectoriel** (celle du popup ask-gate) sur tous les badges "ILLIMITÉ"

### Palette couleur harmonisée
Code couleur systématisé sur tout le site :
- 🟢 **Vert teal** (`#0E5552`, `#C4D3C5`, `#EAF0EA`) : branding général, info, cadres, bordures
- 🟣 **Violet** (`#A855F7`, `#7c3aed`, `#F5EEFE`) : action premium (CTA Focus Illimité, checkmarks, badges verrouillage, highlights "+300 places")
- ⚫ **Suppression du doré/gold** partout (jugé daté / "ChatGPT paywall")

Zones rebrandées :
- Popup paywall (d'or/teal → blanc/vert/violet)
- Popup "Demander à Amélie" (d'or + orange → blanc/vert/violet)
- Animation "Amélie réfléchit" (d'or → violet)
- Card "Bravo" fin de quiz (crème/or → vert pâle)
- Score card mid/good (beige → vert)
- Badges 👑 ILLIMITÉ (doré → violet)
- Boutons quiz "Demander à Amélie" + "voir plus" (doré → violet)

## 📣 Neuromarketing

### Homepage
- **Restauration** du design minimaliste initial (hero serif "Teste un dossier gratuitement. Vois la différence.")
- **Bandeau top** raccourci + axé enjeu : "🎯 Mieux classé = plus de choix sur ta ville et ta spé" (au lieu de l'offre produit)
- **Eyebrow** "Plateforme n°1 EDN" supprimée (trop lourde)
- **Social proof** : 4 avatars en faces réalistes de jeunes adultes via pravatar.cc (style thispersondoesnotexist, URLs stables)
- **Section "Comment ça marche"** refondue avec sous-titre + 3 étapes orientées bénéfice :
  1. "Teste un vrai dossier." (découverte)
  2. "Vois ce que ça t'apporte." (compréhension)
  3. "Transforme ça en choix." (avantage concret → spé + ville d'internat)

### Fin de quiz
- **Card "Bravo"** avec stakes : "Aux EDN, chaque place compte", +300 places en moyenne précisé par **"grâce à la mémorisation répétée"**
- **Score headline** "Tu progresses." en violet
- **Bilan Amélie** (anonyme) avec analyse locale intelligente :
  - Partie visible : intro + "À retravailler en priorité :"
  - Partie floutée (2 lignes exactement) : noms des thèmes faibles + recommandations
  - Effet neuromarketing : on tease la priorité sans révéler les thèmes → push signup
- **Animation "Amélie réfléchit"** en violet (avatar glow, dots, progress bar)
- **CTA principal** : "Passer à Focus Illimité pour voir ton bilan personnalisé →" (au lieu d'un simple "Voir ton bilan complet")

### Popup paywall refondue (moderne)
Remplace l'ancien design bannière gold/teal par un modal épuré :
- Card blanche avec shadow vert teal
- Close X en haut à droite
- Icône cadenas dans cercle vert pâle
- 4 arguments dans pills vert clair avec checkmarks violet :
  - **Toutes les années** d'annales (2016-2025)
  - **Tes corrigés complets** : Amélie t'explique chaque question
  - **Ton bilan personnalisé** détaillé après chaque dossier
  - **+300 places en moyenne** au classement pour nos élèves
- Bouton CTA violet full-width "Passer à Focus Illimité →"
- Lien "Plus tard" en bas (ou "Se connecter" sur homepage)
- **Titre dynamique** selon contexte : "ECN 2017", "EDN 2024", "Ton bilan personnalisé", etc.

### Popup "Demander à Amélie" (ask-gate)
- Card blanche, épurée, branding cohérent
- Tag "RÉSERVÉ AU FOCUS ILLIMITÉ" en violet
- Couronne SVG vert dans cercle vert pâle
- Titre avec accent gradient vert sur "ton assistante médicale"
- Chat preview en vert (bordures, dots, bubbles)
- Lock pill "FOCUS ILLIMITÉ" en violet
- CTA principal violet

### Gate signup (firstQGate)
- Titre : "Tu veux qu'Amélie enregistre ton résultat ?"
- Sous-titre : "Retrouve tes résultats et continue sur d'autres dossiers."
- Bouton : "Suivant →"
- Distingue bien le free (résultat) vs Focus Illimité (corrigé)

## 🔗 Paywall étendu

Tous les CTA Focus Illimité ouvrent maintenant la popup paywall refondue (au lieu d'aller direct à Stripe ou au signup gate) :

- Clic sur années verrouillées (2017-2025) en homepage → popup avec titre ECN/EDN + année
- Clic sur années verrouillées dans le year switcher (dashboard) → popup
- Clic sur dossiers verrouillés 2016 (autres que le gratuit) → popup
- Clic sur exams verrouillés (DCP 2+, QI, LCA) → popup
- Clic sur "voir plus" dans une correction verrouillée → popup
- Status pill dashboard "Compte gratuit · ECN 2016 ouvert" → cliquable → popup
- Bouton "Passer au plan Focus Illimité : 29,90 € / mois" (dashboard) → popup

## 🐛 Bugs corrigés

- **Clamp 2 lignes** sur explication verrouillée : forcé avec `!important` + `-webkit-line-clamp`
- **Alignement des feat pills** : `strong`/`em` ne restaient plus sur lignes séparées (fix flex avec `<span>` wrapper)
- **Clic année verrouillée** : `pointer-events:none` sur le tip hover qui bloquait parfois
- **Popup voir plus ne s'ouvrait plus** : fix du CSS early-hide qui cachait `#paywall` via la whitelist body-only (ajout de `:not(#paywall):not(.paywall)`)

## 🛠️ Outils dev

- **Shortcut `?endquiz=1`** : raccourci URL pour atterrir directement sur l'écran de bilan (4/10 bonnes réponses, Cardiologie en faiblesse) sans faire tout le quiz. Pratique pour itérer sur le funnel de fin.

## 📦 Total

**43 commits** sur `main`, tous pushés sur `origin/main` et redéployés automatiquement sur Vercel (https://ecn.askamelie.com).
