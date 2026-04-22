# Migration couleur — Clinical Green palette

Document dédié au changement de palette du 22 avril 2026 soir : **remplacement complet du violet par le vert flash `#00E676`**.

**Commit** : `8b18848` sur `main`
**Fichiers touchés** : `index.html`, `ecn-focus.html` (98 occurrences remplacées)

---

## 🎨 Nouvelle palette "Clinical Green"

### Vue d'ensemble

| Rôle | Couleur | Hex | HSL | Usage |
|---|---|---|---|---|
| **Vert principal** | Teal profond | `#0E5552` (existant) + `#2E8B7B` (nouveau secondaire) | 171°, 50%, 36% | Nav, titres, icônes principales, identité |
| **Vert clair** | Menthe pâle | `#E8F4F1` / `#EAF0EA` | 168°, 31%, 93% | Fonds de cards, sections alternées, pills info, surlignage neutre |
| **Vert flash** | Vert vif | `#00E676` | 145°, 100%, 45% | **CTA uniquement** (Valider, Continuer), feedback réussite, progression |

> ⚠️ **Règle** : le vert flash est **le seul élément qui doit "claquer" visuellement**. Il est réservé exclusivement aux CTA et aux retours de succès. Ne pas l'utiliser pour de la décoration ou des bordures générales.

---

## 🔄 Table de correspondance (avant → après)

Remplacements effectués par `perl -pi -e "s/.../.../g"` sur les 2 fichiers :

| Ancien (violet) | Nouveau (vert) | Contexte |
|---|---|---|
| `#A855F7` | `#00E676` | Main violet → vert flash |
| `#7c3aed` | `#00B85E` | Violet foncé → vert flash foncé (gradient end, hover text) |
| `#6B2FD6` | `#00A050` | Violet hover state → vert flash hover |
| `rgba(168,85,247,X)` | `rgba(0,230,118,X)` | Shadows et highlights (X = opacité) |
| `rgba(124,58,237,X)` | `rgba(0,184,94,X)` | Gradient variantes |
| `#F5EEFE` | `#E8F4F1` | Background lavande pill → menthe pâle |
| `#EDE4FA` | `#C8E8D8` | Bordure lavande → menthe |
| `#FBF7FE` | `#F0F9F4` | Hover background lavande → menthe |
| `#D9C3F7` | `#9FDCBF` | Bordure tag premium lavande → menthe |

---

## 🎯 Éléments impactés

### CTA principaux (ex-violet → vert flash `#00E676`)

| Élément | Fichier | Usage |
|---|---|---|
| **Bouton paywall** "Passer à Focus Illimité →" | `ecn-focus.html`, `index.html` | CTA popup principal |
| **Bouton ask-gate** "Passer au plan Focus Illimité" | `ecn-focus.html` | CTA popup Demander à Amélie |
| **Bouton fin de quiz** "Passer à Focus Illimité pour voir ton bilan…" | `ecn-focus.html` | CTA card de fin |
| **Bouton "Demander à Amélie"** (feedback quiz) | `ecn-focus.html` | CTA inline dans correction |
| **Lien "voir plus"** (correction verrouillée) | `ecn-focus.html` | CTA texte vers paywall |

### Checkmarks ✓ (pills arguments)

Tous les ✓ dans les pills d'arguments des popups passent de violet à vert flash :
- Popup paywall (`.pw-feats li::before`)
- Card fin de quiz (`.report-unlock-feats li::before`)

### Badges verrouillage

| Badge | Fichier | Gradient |
|---|---|---|
| `👑 ILLIMITÉ` année verrouillée | `.year-card.locked::after` | linear-gradient(135deg, #00E676, #00B85E) |
| `👑 ILLIMITÉ` year switcher | `.ys-card.locked::after` | linear-gradient(135deg, #00E676, #00B85E) |
| `👑` dossier verrouillé | `.dossier-card .lock-badge` | linear-gradient(135deg, #00E676, #00B85E) |
| Icône 🔒 dossier 2016 | JS inline style | color: #00B85E |

### Accents texte / highlights

| Élément | Usage |
|---|---|
| Headline score "Tu progresses." / "Excellent travail." | Vert flash `#00B85E` |
| Highlight surligneur "+300 places" | `rgba(0,230,118,.3)` |
| Accent titre "ton assistante médicale" (ask-gate) | Vert gradient `#0E5552 → #1B6E68` (inchangé) |
| Contour pointillé stakes "EDN 60% · ECOS 30%…" | `2px dashed #00E676` |

### Elements chat ask-gate

Partie violet avant → vert maintenant :
- Lock pill "FOCUS ILLIMITÉ" en bas du chat preview
- Tag "RÉSERVÉ AU FOCUS ILLIMITÉ" (pill vert pâle `#E8F4F1` / bordure `#9FDCBF`)

### Hovers & états

Tous les hovers violet (ex: `.q-voir-plus:hover{color:#6B2FD6}`) passent en vert foncé `#00A050`.

---

## 🔍 Règle visuelle simplifiée

**1 écran = max 1 élément en vert flash #00E676 actif visible à la fois.**

Le vert flash ne doit jamais être décoratif, toujours actionable :
- ✅ Un CTA principal visible sur la page
- ✅ Un checkmark ✓ dans une liste d'arguments
- ✅ Un feedback "Bonne réponse !"
- ❌ Pas de texte général en vert flash
- ❌ Pas de bordure ou fond coloré en vert flash (trop agressif)

Pour les éléments secondaires : utiliser le **vert teal `#0E5552`** (principal) ou le **vert sage `#EAF0EA`** (backgrounds).

---

## 🚨 Vérifications à faire après migration prod

1. **Contraste AA** : vérifier que le vert flash `#00E676` sur fond blanc respecte AA pour le texte (ratio ≥ 4.5:1). Si pas assez contrasté sur les boutons avec texte blanc, utiliser `#00B85E` à la place pour le background.
2. **Cohérence** : s'assurer qu'aucun élément décoratif n'utilise le vert flash (réservé action uniquement).
3. **SVG crown** : les badges ILLIMITÉ utilisent un SVG data URI avec `fill="%2300B85E"` (vert foncé pour le crown sur fond transparent des pills responsives).

---

## 📦 Reproduction de la migration côté prod

Commande perl pour faire le replacement en bulk (à exécuter à la racine du projet) :

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

## 🎨 Avant / Après visuel

### CTA paywall

**Avant** :
```
[  Passer à Focus Illimité →  ]  ← bg: gradient violet A855F7→7c3aed
                                    shadow: rgba(168,85,247,.42)
```

**Après** :
```
[  Passer à Focus Illimité →  ]  ← bg: gradient vert flash 00E676→00B85E
                                    shadow: rgba(0,230,118,.42)
```

### Checkmarks arguments

**Avant** : cercle violet `#A855F7` → `#7c3aed`, blanc check ✓
**Après** : cercle vert flash `#00E676` → `#00B85E`, blanc check ✓

### Badge couronne "ILLIMITÉ"

**Avant** : pill violet gradient avec couronne SVG blanche
**Après** : pill vert flash gradient avec couronne SVG blanche (même structure, juste la couleur)

---

## 📄 Référence

- Commit GitHub : [`8b18848`](https://github.com/Yanchan7m/ASKAMELIE-TEST-MAQUETTE/commit/8b18848)
- Fichiers source : `ecn-focus.html`, `index.html`
- Doc complète : `MIGRATION-PROD.md` (guide global des 43 commits de la journée)
