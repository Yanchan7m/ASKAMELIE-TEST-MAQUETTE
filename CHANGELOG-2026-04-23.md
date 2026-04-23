# Changelog — 23 avril 2026

Journée centrée sur le **logo wordmark**, la **réassurance pré-paiement** ("trust block"), et plusieurs **polish design** (fonds blancs, section science, carrousel témoignages, fix scroll fin de quiz). Tous les changements sont dans la maquette `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main` et doivent être portés sur la codebase prod.

Commits couverts : de `a4125a4` à `2ce91c0` (17 commits code + 4 commits doc).

---

## 🎨 1. Logo wordmark "Askamelie" — passage en sans-serif

Commits : `a4125a4` → `cb79af3` → `b619257`

**Avant** : Libre Baskerville serif, 22px, font-weight 800, "amélie" en italique salmon.
**Après** : Work Sans **sans-serif**, **17px**, **font-weight 600** (semi-bold, pas de bold lourd), letter-spacing `-.02em`. Texte sans accent : "amelie" (pas "amélie") pour matcher `promo.askamelie.com`.

### CSS cible

```css
.brand{
  display:inline-flex; align-items:baseline;
  font-family:'Work Sans','Inter',-apple-system,sans-serif;
  font-size:17px; font-weight:600; letter-spacing:-.02em;
  text-decoration:none; line-height:1;
}
.brand .mark{display:none}
.brand .title{
  font-family:'Work Sans','Inter',-apple-system,sans-serif;
  font-size:17px; font-weight:600; letter-spacing:-.02em;
  color:#0F6B5B;
}
.brand .title em{font-style:normal; color:#E89C6E; font-weight:600}
```

### HTML

```html
<a href="index.html" class="brand">
  <span class="mark">A</span>  <!-- caché via display:none -->
  <span class="title">Ask<em>amelie</em></span>
</a>
```

### Font loader Google Fonts (ajout weight 800 au cas où)

```html
<link href="https://fonts.googleapis.com/css2?family=Libre+Baskerville:ital,wght@0,400;0,700;1,400&family=Work+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
```

**Propagation** : s'applique au nav header de `index.html`. Les blocs footer + ecn-focus top bar gardent leur structure (pas touchés dans cette passe).

---

## ⚪ 2. Fonds beiges `#EEE7DC` → blanc `#fff`

Commit : `d6487aa` + `34e81f2`

Le beige `#EEE7DC` reste dans la palette (accent) mais est **cantonné aux petits éléments décoratifs** (icônes paywall, cards de correction pendant un quiz, états `.done-*` de dossiers). Les **gros conteneurs de fond** passent en blanc.

### Sélecteurs touchés

| Fichier | Sélecteur | Avant | Après |
|---|---|---|---|
| `index.html` | `.hero-stats` | `background:#EEE7DC` | `background:#fff` |
| `index.html` | `.steps-section` | `background:#EEE7DC` | `background:#fff` |
| `ecn-focus.html` | `.year-switcher` | `background:#EEE7DC` | `background:#fff` |
| `ecn-focus.html` | `.ex-section-hdr` | `background:#EEE7DC` | `background:#fff` |
| `ecn-focus.html` | `.dossier-card` (base) | `background:#FAF8F3` | `background:#fff` |
| `ecn-focus.html` | `.dossier-card.locked` | `background:#F7F5F2` | `background:#fff` (opacity .62 conservée) |

Bordures `#DDD3C3` conservées partout pour garder la définition visuelle.

### Perl reproductible

```bash
perl -pi -e "
  s|(\.hero-stats\{[^}]*?background:)#EEE7DC|\${1}#fff|g;
  s|(\.steps-section\{[^}]*?background:)#EEE7DC|\${1}#fff|g;
  s|(\.year-switcher\{[^}]*?background:)#EEE7DC|\${1}#fff|g;
  s|(\.ex-section-hdr\{[^}]*?background:)#EEE7DC|\${1}#fff|g;
  s|(\.dossier-card\{background:)#FAF8F3|\${1}#fff|g;
  s|(\.dossier-card\.locked\{opacity:\.62;background:)#F7F5F2|\${1}#fff|g;
" index.html ecn-focus.html
```

**À NE PAS TOUCHER** : `.q-context`, `.q-explanation`, `.q-opt.selected`, `.pw-lock`, `.pw-feats li`, `.report-unlock-*`, `.ask-gate-*`, `.auth-banner` (gradient subtil), `.dossier-card.done-mastered`, `.dossier-card.done-weak`. Ces beiges sont sémantiques.

---

## 🛡️ 3. Trust block "Amélie conçue pour ne pas se tromper" (NOUVEAU — 8 emplacements)

Commits : `259323c` → `3f042cc` → `e3eea2e` → `ac860ca` → `e44a927` + `2ce91c0` (em-dashes)

Bloc de réassurance ajouté **avant chaque CTA de paiement** pour renforcer la valeur perçue et le taux de conversion. Wording arbitré comme **défendable DGCCRF / art. L.121-1** (pratiques commerciales trompeuses) : on décrit le **process de validation** et l'**intention** du système, pas une infaillibilité absolue.

### 3.1 Wording final (copy validée, EXACTE)

```
[badge bouclier]   Amélie a été conçue pour ne pas se tromper.

[icône équipe]     800+ médecins et professeurs spécialistes
                   Tous ont validé le raisonnement clinique d'Amélie.

[icône check×2]    Double vérification systématique
                   Chaque correction est croisée avec les référentiels
                   officiels : CUESPB, CNEMV, collèges de spécialité.

[icône livre]      10 ans d'annales EDN/ECN
                   Toutes corrigées manuellement par des spécialistes
                   avant d'entraîner Amélie.

[icône check○]     Une fiabilité clinique que tu ne trouveras nulle part
                   ailleurs. Tu peux t'appuyer dessus en toute confiance.
```

### ⚠️ Wordings INTERDITS (risque DGCCRF + contre-productif cible médicale)

- ❌ "Amélie ne se trompe jamais"
- ❌ "Il est impossible qu'elle se trompe"
- ❌ "Fiabilité 100%" / "garantie d'exactitude"

Le "**conçue pour ne pas se tromper**" décrit une intention de design, pas une garantie → défendable.

### 3.2 Structure HTML (design v2 — "stat + description")

```html
<div class="trust-amelie">
  <div class="trust-amelie-head">
    <div class="trust-amelie-badge">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/>
        <path d="M9 12l2 2 4-4"/>
      </svg>
    </div>
    <div class="trust-amelie-title">Amélie a été conçue pour <strong>ne pas se tromper</strong>.</div>
  </div>
  <div class="trust-amelie-grid">
    <div class="trust-amelie-item">
      <div class="trust-amelie-item-icon">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"/>
          <circle cx="9" cy="7" r="4"/>
          <path d="M23 21v-2a4 4 0 0 0-3-3.87"/>
          <path d="M16 3.13a4 4 0 0 1 0 7.75"/>
        </svg>
      </div>
      <div class="trust-amelie-item-body">
        <div class="trust-amelie-item-stat">800+ médecins et professeurs spécialistes</div>
        <div class="trust-amelie-item-desc">Tous ont validé le raisonnement clinique d'Amélie.</div>
      </div>
    </div>
    <div class="trust-amelie-item">
      <div class="trust-amelie-item-icon">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <polyline points="20 6 9 17 4 12"/>
          <polyline points="14 17 18 21 22 17"/>
        </svg>
      </div>
      <div class="trust-amelie-item-body">
        <div class="trust-amelie-item-stat">Double vérification systématique</div>
        <div class="trust-amelie-item-desc">Chaque correction est croisée avec les référentiels officiels : CUESPB, CNEMV, collèges de spécialité.</div>
      </div>
    </div>
    <div class="trust-amelie-item">
      <div class="trust-amelie-item-icon">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M2 3h6a4 4 0 0 1 4 4v14a3 3 0 0 0-3-3H2z"/>
          <path d="M22 3h-6a4 4 0 0 0-4 4v14a3 3 0 0 1 3-3h7z"/>
        </svg>
      </div>
      <div class="trust-amelie-item-body">
        <div class="trust-amelie-item-stat">10 ans d'annales EDN/ECN</div>
        <div class="trust-amelie-item-desc">Toutes corrigées manuellement par des spécialistes avant d'entraîner Amélie.</div>
      </div>
    </div>
  </div>
  <div class="trust-amelie-foot">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round">
      <circle cx="12" cy="12" r="10"/>
      <polyline points="9 12 11 14 15 10"/>
    </svg>
    <span>Une fiabilité clinique que tu ne trouveras nulle part ailleurs. Tu peux t'appuyer dessus en toute confiance.</span>
  </div>
</div>
```

### 3.3 CSS

```css
.trust-amelie{max-width:640px;margin:32px auto 0;padding:28px 32px;background:#fff;border:1px solid #DDD3C3;border-radius:16px;text-align:left;box-shadow:var(--shadow-sm)}
.trust-amelie-head{display:flex;align-items:center;gap:14px;padding-bottom:20px;margin-bottom:20px;border-bottom:1px solid #EEE7DC}
.trust-amelie-badge{flex-shrink:0;width:44px;height:44px;border-radius:12px;background:linear-gradient(135deg,#0F6B5B 0%,#1B4D3E 100%);color:#fff;display:flex;align-items:center;justify-content:center;box-shadow:0 4px 12px rgba(15,107,91,.28)}
.trust-amelie-badge svg{width:22px;height:22px}
.trust-amelie-title{font-family:var(--font-serif);font-size:17px;font-weight:700;color:var(--fg);line-height:1.35;letter-spacing:-.01em}
.trust-amelie-title strong{color:var(--primary-darkest);font-weight:700}
.trust-amelie-grid{display:flex;flex-direction:column;gap:18px}
.trust-amelie-item{display:flex;gap:14px;align-items:flex-start}
.trust-amelie-item-icon{flex-shrink:0;width:36px;height:36px;border-radius:10px;background:var(--sage-soft);color:var(--primary-darkest);display:flex;align-items:center;justify-content:center}
.trust-amelie-item-icon svg{width:18px;height:18px}
.trust-amelie-item-body{flex:1;min-width:0}
.trust-amelie-item-stat{font-size:14.5px;font-weight:700;color:var(--primary-darkest);margin-bottom:3px;letter-spacing:-.005em;line-height:1.35}
.trust-amelie-item-desc{font-size:13.5px;color:var(--fg-soft);line-height:1.5}
.trust-amelie-foot{margin-top:22px;padding-top:18px;border-top:1px solid #EEE7DC;display:flex;align-items:flex-start;gap:10px;font-size:13.5px;color:var(--fg);line-height:1.55;font-weight:500}
.trust-amelie-foot svg{flex-shrink:0;width:18px;height:18px;color:var(--primary);margin-top:1px}

/* Variant dark pour .premium-cta (fond vert foncé) */
.premium-cta .trust-amelie{background:rgba(255,255,255,.06);border-color:rgba(255,255,255,.15);margin:24px auto 0;backdrop-filter:blur(6px);-webkit-backdrop-filter:blur(6px);position:relative;box-shadow:none}
.premium-cta .trust-amelie-head{border-bottom-color:rgba(255,255,255,.15)}
.premium-cta .trust-amelie-badge{background:linear-gradient(135deg,var(--accent) 0%,var(--accent-dark) 100%);box-shadow:0 4px 12px rgba(232,156,110,.4)}
.premium-cta .trust-amelie-title{color:#fff}
.premium-cta .trust-amelie-title strong{color:var(--accent)}
.premium-cta .trust-amelie-item-icon{background:rgba(255,255,255,.08);color:var(--accent)}
.premium-cta .trust-amelie-item-stat{color:#fff}
.premium-cta .trust-amelie-item-desc{color:rgba(255,255,255,.75)}
.premium-cta .trust-amelie-foot{border-top-color:rgba(255,255,255,.15);color:rgba(255,255,255,.9)}
.premium-cta .trust-amelie-foot svg{color:var(--accent)}

/* Responsive */
@media(max-width:600px){.trust-amelie{padding:22px 22px;margin:24px 18px 0}.trust-amelie-badge{width:38px;height:38px}.trust-amelie-badge svg{width:19px;height:19px}.trust-amelie-title{font-size:15px}.trust-amelie-item-stat{font-size:13.5px}.trust-amelie-item-desc{font-size:12.5px}.trust-amelie-foot{font-size:12.5px}}
```

### 3.4 Emplacements (8 au total)

| # | Fichier | Contexte | Position | Design |
|---|---|---|---|---|
| 1 | `index.html` | Hero homepage | Après `.hero-proof`, fin de `<section class="hero">` | v2 |
| 2 | `index.html` | Pricing card | Entre `.pricing-feats` et `.pricing-actions` | v2 |
| 3 | `index.html` | `.premium-cta` footer (fond vert foncé) | Avant `<button class="premium-cta-btn">` | v2 dark |
| 4 | `ecn-focus.html` | Paywall modal `#paywall` | Entre `.pw-feats` et `.pw-cta-gold` | v2 |
| 5 | `ecn-focus.html` | **Ask-gate modal `#askGate`** | Avant `.ask-gate-cta` | **v1 compact** |
| 6 | `ecn-focus.html` | Offer card "Focus Illimité" (section `.offers`) | Entre `.offer-feats` et `.offer-cta` | v2 |
| 7 | `ecn-focus.html` | Conv-block fin de quiz authed non-premium (JS `renderScore`) | Entre `.conv-sub` et `<button class="conv-btn-primary">` | v2 |
| 8 | `ecn-focus.html` | `.report-unlock` fin de quiz anon (JS template injecté après animation) | Entre `.report-unlock-feats` et `.report-unlock-btn` | v2 |

### 3.5 Ask-gate = version compact (v1) — classes renommées

Le modal "Demander à Amélie" est déjà dense (tag + titre + chat preview 3 messages). Le design v2 y serait trop volumineux. On garde une v1 compacte sous un préfixe de classes dédié pour ne pas entrer en conflit avec le `.trust-amelie*` v2 global.

**HTML ask-gate uniquement** :

```html
<div class="trust-amelie-compact">
  <div class="trust-amelie-compact-head">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round">
      <path d="M9 12l2 2 4-4"/>
      <path d="M21 12c0 4.97-4.03 9-9 9s-9-4.03-9-9 4.03-9 9-9c2.35 0 4.5.9 6.1 2.4"/>
    </svg>
    Amélie a été conçue pour ne pas se tromper.
  </div>
  <ul class="trust-amelie-compact-feats">
    <li><svg><path d="M20 6L9 17l-5-5"/></svg>Elle représente l'équivalent de <strong>800+ médecins et professeurs spécialistes</strong> qui ont validé son raisonnement.</li>
    <li><svg><path d="M20 6L9 17l-5-5"/></svg>Chaque correction est <strong>croisée et doublement vérifiée</strong> avec les référentiels officiels (CUESPB, CNEMV, collèges de spécialité).</li>
    <li><svg><path d="M20 6L9 17l-5-5"/></svg>Entraînée sur <strong>10 ans d'annales EDN/ECN</strong> corrigées manuellement par des spécialistes.</li>
  </ul>
  <div class="trust-amelie-compact-foot">Une fiabilité clinique que tu ne trouveras nulle part ailleurs. Tu peux t'appuyer dessus en toute confiance.</div>
</div>
```

**CSS compact** (à côté du CSS v2 dans `ecn-focus.html` uniquement) :

```css
.trust-amelie-compact{margin:14px auto;padding:16px 18px;background:#fff;border:1px solid #DDD3C3;border-radius:14px;text-align:left}
.trust-amelie-compact-head{display:flex;align-items:center;gap:9px;margin-bottom:11px;font-family:'Libre Baskerville',Georgia,serif;font-size:14.5px;color:#163A3A;font-weight:700;letter-spacing:-.01em;line-height:1.3}
.trust-amelie-compact-head svg{flex-shrink:0;width:19px;height:19px;color:#0F6B5B}
.trust-amelie-compact-feats{list-style:none;padding:0;margin:0;display:flex;flex-direction:column;gap:7px}
.trust-amelie-compact-feats li{display:flex;align-items:flex-start;gap:8px;font-size:12.5px;color:#163A3A;line-height:1.5}
.trust-amelie-compact-feats li svg{flex-shrink:0;width:14px;height:14px;color:#0F6B5B;margin-top:2px}
.trust-amelie-compact-feats li strong{color:#0F6B5B;font-weight:700}
.trust-amelie-compact-foot{margin-top:10px;padding-top:8px;border-top:1px dashed #DDD3C3;font-size:12px;color:#527A70;font-style:italic;line-height:1.5}
```

---

## 🧠 4. Section science — "Pourquoi t'entraîner bat le fait de relire"

Commits : (intégré dans la série trust block) + `b99c83a` (badge retiré) + `a7fcaf6` (wording final).

Nouvelle section `index.html` **entre `.steps-section` et `.years-section`**. Argumente la méthodologie par l'**effet de test** (testing effect) — concept documenté en neurosciences cognitives (Roediger & Karpicke, Dunlosky) que les étudiants en médecine reconnaissent comme solide.

### HTML

```html
<section class="science-section">
  <div class="science-inner">
    <h2>Pourquoi <span class="underline">t'entraîner</span> bat toujours le fait de relire ton cours.</h2>
    <div class="science-body">
      <p>Les neurosciences et la psychologie de l'apprentissage parlent de l'<strong>« effet de test »</strong> (<em>testing effect</em>) : rappeler régulièrement une information (par exemple via un quiz ou un QCM) <strong>renforce sa consolidation dans la mémoire à long terme</strong>.</p>
      <p>Les synthèses et méta‑analyses sont unanimes : les étudiants qui s'entraînent avec des mini‑quiz / QCM obtiennent <strong>systématiquement de meilleures performances</strong> que ceux qui se contentent de relire leur cours.</p>
      <div class="science-quote">
        <p>C'est exactement ce que fait Amélie : elle te met face à de vrais dossiers EDN/ECN, te corrige avec le raisonnement d'un spécialiste, et garde la trace de <strong>tes</strong> faiblesses pour te re-tester au bon moment.</p>
      </div>
    </div>
  </div>
</section>
```

**⚠️ Pas d'eyebrow "Basé sur les neurosciences"** — initialement présente, retirée car trop lourde (le contenu du H2 parle déjà du sujet).

### CSS

```css
.science-section{max-width:1100px;margin:56px auto 0;padding:0 24px}
.science-inner{background:#fff;border:1px solid #DDD3C3;border-radius:18px;padding:44px 48px;text-align:left;position:relative;box-shadow:var(--shadow-sm)}
.science-section h2{font-family:var(--font-serif);font-size:clamp(24px,3.4vw,34px);font-weight:700;color:var(--fg);letter-spacing:-.015em;line-height:1.2;margin-bottom:22px}
.science-section h2 .underline{position:relative;display:inline-block;color:var(--primary-darkest)}
.science-section h2 .underline::after{content:'';position:absolute;left:0;right:0;bottom:4%;height:40%;background:rgba(232,156,110,.38);z-index:-1;border-radius:2px}
.science-body p{font-size:15.5px;color:var(--fg);line-height:1.7;margin-bottom:14px}
.science-body p:last-child{margin-bottom:0}
.science-body strong{color:var(--primary-darkest);font-weight:700}
.science-body em{font-style:italic;color:var(--fg-soft)}
.science-quote{margin-top:22px;padding:18px 22px;background:var(--sage-soft);border-left:3px solid var(--primary);border-radius:10px}
.science-quote p{margin:0;font-size:15px;color:var(--fg);font-style:italic;line-height:1.6}
.science-quote p strong{font-style:normal}
@media(max-width:720px){.science-inner{padding:32px 26px}.science-body p{font-size:14.5px}.science-quote{padding:16px 18px}.science-quote p{font-size:14px}}
```

---

## ⭐ 5. Témoignages — carrousel auto-scroll × 10 avis

Commits : `4de663d` + `6801037`

Section testimonials transformée d'un **grid statique 4 colonnes** à un **marquee horizontal auto-scroll** (pause au survol, respect de `prefers-reduced-motion`) avec 10 avis qui défilent en continu. Mix de **4 alumni Promo 2025** (ont passé l'EDN en juin 2025 = track-record prouvé) + **6 D4 actuels** (en préparation).

### 10 témoignages

| # | Prénom | Statut | Ville | Photo | Angle |
|---|---|---|---|---|---|
| 1 | Léa D. | D4 · Top 500 | Paris Descartes | `img/students/student_lea.png` | Raisonnement vs PDFs |
| 2 | Antoine M. | **Promo 2025** · Top 800 | Bordeaux | `pravatar?img=12` | Qualité corrigé |
| 3 | Sofia R. | **Promo 2025** · Dermatologie | Toulouse | `pravatar?img=47` | Bilans personnalisés |
| 4 | Thomas L. | D4 · Top 400 | Lyon Est | `pravatar?img=33` | Onglet "Demander à Amélie" |
| 5 | Clara B. | **Promo 2025** · Radiologie | Strasbourg | `pravatar?img=45` | IRM/ECG analysés |
| 6 | Hugo V. | D4 | Lille | `pravatar?img=14` | LCA : 7→16 en 3 mois |
| 7 | Mathilde G. | D4 | Rennes | `pravatar?img=23` | Redoublement · méthode |
| 8 | **Julie M.** | D4 | Angers | **Sans photo — initiales JM** | **Répétition des tests → mémorisation** (écho section 4) |
| 9 | Pauline D. | **Promo 2025** | Nantes | `pravatar?img=44` | Gain de temps 12h→7h |
| 10 | Lucas T. | D4 | Montpellier | `pravatar?img=68` | Personnalisation pharmaco |

### Avatar sans photo (Julie M.)

```html
<div class="testi-avatar-initials" aria-label="Julie M.">JM</div>
```

```css
.testi-avatar-initials{width:40px;height:40px;border-radius:50%;background:var(--sage-soft);color:var(--primary-darkest);display:flex;align-items:center;justify-content:center;font-family:var(--font-serif);font-size:14px;font-weight:700;letter-spacing:-.01em;border:1px solid var(--sage);flex-shrink:0}
```

### Structure marquee

```html
<section class="testimonials">
  <div class="testimonials-header">
    <h2>Ils ont décroché la spé de leur <span class="underline">choix</span>.</h2>
  </div>
  <div class="testi-marquee">
    <div class="testi-track" id="testiTrack">
      <!-- 10 <div class="testi-card">...</div> -->
    </div>
  </div>
  <script>
    /* Duplique les cards pour un loop seamless */
    (function(){
      var track = document.getElementById('testiTrack');
      if (track) track.insertAdjacentHTML('beforeend', track.innerHTML);
    })();
  </script>
  <div class="testi-bottom">...</div>
</section>
```

### CSS marquee

```css
.testi-marquee{overflow:hidden;position:relative;margin:0 -24px;padding:4px 0;-webkit-mask-image:linear-gradient(to right,transparent 0,#000 6%,#000 94%,transparent 100%);mask-image:linear-gradient(to right,transparent 0,#000 6%,#000 94%,transparent 100%)}
.testi-track{display:flex;gap:18px;padding:4px 24px 4px 24px;width:max-content;animation:testiScroll 90s linear infinite}
.testi-marquee:hover .testi-track,.testi-marquee:focus-within .testi-track{animation-play-state:paused}
.testi-track .testi-card{flex:0 0 340px;width:340px}
@keyframes testiScroll{from{transform:translateX(0)}to{transform:translateX(calc(-50% - 9px))}}
@media(prefers-reduced-motion:reduce){.testi-track{animation:none}}
```

**Note math** : `calc(-50% - 9px)` compense la moitié du gap inter-set (18px / 2) pour un loop sans saccade quand le track est dupliqué via JS.

---

## ⬆️ 6. Fin de quiz — scroll-to-top effectif

Commit : `9de4b61`

**Bug** : à la fin d'un quiz (ou via le shortcut `?endquiz=1`), l'utilisateur atterrissait **au milieu de l'animation "Amélie réfléchit"** au lieu du haut du bilan (card `ECN 2016 · Dossier démo cardio · Bravo · 40%`). Le `scrollTo({top:0})` dans `renderScore()` s'exécutait **avant** `body().innerHTML = h`, donc avant que le DOM n'existe → sans effet visuel.

**Fix** : scroll-to-top **après** l'injection HTML, redéclenché dans `requestAnimationFrame` pour couvrir les re-layouts asynchrones (sticky CTA append au body, paint du dashboard en `setTimeout 150ms`).

### Emplacement dans `ecn-focus.html`

Dans `renderScore()`, juste après le bloc `if (!isPremium)` qui append le sticky CTA au body :

```js
// Scroll-to-top après rendu : garantit que l'utilisateur atterrit en
// haut du bilan (card "Bravo" + score) et pas au milieu de l'animation
// "Amélie réfléchit". Le scroll avant body().innerHTML est inefficace
// car le contenu n'existe pas encore, et certains navigateurs
// restaurent une position après l'injection. On le redéclenche ici
// + une fois en fin de frame pour couvrir les layouts async.
try {
  var ovTop = document.getElementById('examOverlay');
  if (ovTop) ovTop.scrollTo({ top: 0, behavior: 'auto' });
  window.scrollTo({ top: 0, behavior: 'auto' });
  requestAnimationFrame(function(){
    if (ovTop) ovTop.scrollTo({ top: 0, behavior: 'auto' });
    window.scrollTo({ top: 0, behavior: 'auto' });
  });
} catch(e){}
```

Le scroll initial en haut de `renderScore()` (avant le build de `h`) est **conservé comme fallback**.

**Test rapide** : ouvrir `/ecn-focus.html?endquiz=1` → doit atterrir sur "ECN 2016 · Dossier démo cardio · Bravo · 40%" sans scroll manuel.

---

## ✂️ 7. Wording — em-dashes "—" supprimés du texte user-facing

Commit : `2ce91c0`

Le caractère em-dash `—` donne un ton "marketing IA généré" sur cible médicale. **10 occurrences** user-facing nettoyées :

| Contexte | Avant | Après |
|---|---|---|
| Trust block foot × 8 | `"ailleurs — tu peux t'appuyer..."` | `"ailleurs. Tu peux t'appuyer..."` (2 phrases) |
| Section science | `"information — par exemple via un QCM — renforce"` | `"information (par exemple via un QCM) renforce"` (parenthèses) |
| Testimonial Pauline D. | `"mieux dormir — et mes notes"` | `"mieux dormir, et mes notes"` (virgule) |

**Non touchés** : les `'—'` en JS utilisés comme placeholders pour valeurs vides (`t.label || '—'`, `#nudEmail` vide, etc.) et les em-dashes dans les commentaires JS.

---

## 🧪 8. Vérifications post-migration (pour le dev prod)

1. Logo : Work Sans weight 600 disponible (déjà dans la config Google Fonts). Police rendue sans-serif, 17px, "Askamelie" (pas d'accent).
2. Beiges `#EEE7DC` bien virés de `.hero-stats`, `.steps-section`, `.year-switcher`, `.ex-section-hdr`, `.dossier-card`, `.dossier-card.locked`.
3. Trust block présent avant les **8 CTAs** listés en section 3.4. Test manuel :
   - Homepage → 3 positions visibles au scroll
   - Cliquer sur une année verrouillée 2017-2025 → paywall modal avec trust block
   - Page exam list → offer card "Focus Illimité" a le trust block
   - Cliquer "Demander à Amélie" dans un quiz → ask-gate avec **version compact**
   - Finir un quiz anonyme → après animation, trust block dans `.report-unlock`
   - Finir un quiz authed non-premium → trust block dans `.conv-block`
4. Section science présente entre "Comment ça marche" et les year cards, **sans** eyebrow "Basé sur les neurosciences".
5. Témoignages défilent en continu à 90s/cycle, pause au survol, **10 cards** visibles dont Julie M. sans photo.
6. `?endquiz=1` → atterrir sur la card `Bravo · 40%` en haut de page, pas sur l'animation "Amélie réfléchit".
7. Recherche `grep "—"` sur les fichiers html → aucune occurrence dans du texte user-facing.

---

## 📦 Fichiers modifiés

- `index.html` (toutes les sections sauf admin/auth)
- `ecn-focus.html` (paywall, ask-gate, offer card, renderScore, CSS trust-amelie + compact)
- `MIGRATION-FINAL.md` (doc consolidée — voir sections 4, 12, 13, 14, 15, 16 pour le contexte long)

**Repo** : `Yanchan7m/ASKAMELIE-TEST-MAQUETTE` branche `main`
**Commits** : `a4125a4` → `2ce91c0` (17 commits code + 4 doc)
