# Audit Design Premium & Refonte du Portfolio
## Doudou Diakite — Mannequin International

**Site analysé :** http://109.199.97.183/portfolio/
**Date de l'audit :** 13 septembre 2026
**Analyses effectuées :** Desktop (1920×1080), Mobile (375×812), lecture du code source HTML complet (480 lignes), inspection des performances navigateur, test des interactions (menu burger, filtres, lightbox, formulaire, bilinguisme)

**Méthodologie et limites :** Le site a été visité et manipulé dans un navigateur Chromium en desktop (1920×1080) et mobile (375×812). Le code source HTML complet a été lu et analysé. Les temps de chargement (DOMContentLoaded, loadEventEnd) ont été relevés via l'API Performance du navigateur. Cependant, cet audit ne comprend pas un test Lighthouse complet ni une analyse détaillée du poids réseau de chaque ressource. Les ratios de contraste sont estimés visuellement et doivent être validés avec un outil dédié (WebAIM Contrast Checker) avant correction.

---

## 1. Audit du design actuel

### 1.1 Points forts observés

| # | Élément | Observation | Impact |
|---|---------|-------------|--------|
| 1 | **Palette de couleurs** | Fond sombre chaleureux (#14100C), texte ivoire (#EDE6D6), accent or (#C6A15B). Cohérent avec l'univers mode/luxe africain. | Identité visuelle immédiatement reconnaissable, ambiance premium. |
| 2 | **Typographie** | Fraunces (serif display, variable) pour les titres + Inter pour le corps. Le serif apporte un caractère éditorial. | Hiérarchie visuelle claire, sensation de magazine de mode. |
| 3 | **Structure de page** | Hero → Comp Card → Polas → Lookbook → Showreel → Contact. Logique professionnelle pour un book mannequin. | Parcours utilisateur intuitif, correspond aux standards du métier. |
| 4 | **Header fixed** | Barre de navigation fixe avec backdrop-blur-md, transparente sur le scroll. | Navigation toujours accessible, aspect moderne. |
| 5 | **Animations d'entrée** | Classes `.rise` avec délais échelonnés (0.05s à 0.55s), courbe cubic-bezier(.16,1,.3,1). | Révélation élégante du contenu au chargement. |
| 6 | **Bilingue FR/EN** | Système data-fr/data-en avec persistance localStorage. Fonctionnel et bien implémenté. | Accessibilité internationale, pertinent pour un mannequin "disponible à l'international". |
| 7 | **Badge "Disponible"** | Pastille verte animée (pulse) + lien vers contact. | Signal commercial fort, incite à l'action. |
| 8 | **Ruler SVG décoratif** | Règle verticale avec graduations et marqueur or à 1,95m. | Détail créatif qui traduit visuellement la stature du mannequin. |
| 9 | **Respect de prefers-reduced-motion** | Désactivation des animations pour les utilisateurs sensibles. | Bonne pratique d'accessibilité. |
| 10 | **Focus-visible** | Outline doré (2px solid #C6A15B) sur les éléments interactifs. | Accessibilité clavier correcte. |

### 1.2 Problèmes identifiés

#### Critiques (bloquants pour une expérience premium)

**P1 — Tailwind CDN en production**
Le site charge `https://cdn.tailwindcss.com/` (version de développement de Tailwind) qui génère le CSS à la volée dans le navigateur via JavaScript. C'est explicitement déconseillé par Tailwind pour la production : le script est volumineux, le CSS généré n'est pas minifié ni optimisé (toutes les classes Tailwind sont incluses, pas seulement celles utilisées), et le rendu est bloqué jusqu'à l'exécution du script. Les temps observés (DOMContentLoaded ~878ms, loadEventEnd ~928ms) incluent ce surcoût.
**Correction :** Compiler Tailwind en CSS statique via `npx tailwindcss build` ou migrer vers un build Vite/PostCSS.

**P2 — Showreel entièrement non fonctionnel**
Les deux emplacements vidéo ("RUNWAY WALK" et "PRÉSENTATION") sont des placeholders `.ph-video` sans aucune vidéo. Pire, le texte d'instruction développeur est visible publiquement : « Remplacer les placeholders par : `<video controls src="videos/walk.mp4">` ou un `<iframe>` YouTube/Vimeo. »
**Correction :** Intégrer de vraies vidéos (YouTube/Vimeo embed ou `<video>` avec sources multiples) et supprimer le texte développeur.

**P3 — Lookbook entièrement placeholder**
Les 9 éléments de la galerie Lookbook sont des placeholders avec des labels génériques ("ATHLETIC — 01", "STREETWEAR — 01", etc.). Aucune photo réelle. Les filtres (TOUT / ATHLETIC / STREETWEAR / HAUTE COUTURE) fonctionnent mais filtrent du vide.
**Correction :** Remplacer tous les placeholders par de vraies photos organisées par catégorie.

**P4 — Polas incomplets**
Sur les 3 emplacements Polas (FACE/FRONT, PROFIL/PROFILE, PLEIN PIED), seuls le plein pied a une vraie photo. Les deux autres sont des placeholders.
**Correction :** Ajouter les photos face et profil sur fond uni clair, en lumière naturelle.

**P5 — Données de contact fictives**
Le nom du manager est "[Nom du manager]", l'email est "booking@example.com", le téléphone est "+223 00 00 00 00". Ces placeholders détruisent toute crédibilité professionnelle.
**Correction :** Remplacer par les vraies coordonnées du management.

#### Importantes (dégradation significative de l'expérience)

**P6 — Lightbox non fonctionnel**
Le lightbox s'ouvre correctement (overlay, bouton fermer, touche Escape) mais affiche un placeholder `.ph` au lieu d'une vraie image agrandie. Le `openLB()` ne fait que copier le data-label du placeholder.
**Correction :** Connecter le lightbox aux vraies images (`<img src="images/look-01.jpg">`) avec navigation précédent/suivant.

**P7 — Aucun SEO**
Pas de balises Open Graph, pas de Schema.org/JSON-LD (Person, Model), pas de canonical URL, pas de favicon. Le titre et la meta description existent mais sont insuffisants pour le partage social et le référencement.
**Correction :** Ajouter OG tags, Twitter Cards, JSON-LD Person, favicon, sitemap.xml.

**P8 — Inter comme police principale**
Inter est listée parmi les polices "overused" (surutilisées) sur le web. Bien que fonctionnelle, elle manque de caractère distinctif pour un portfolio premium de mannequin.
**Correction :** Remplacer par Satoshi, General Sans, ou DM Sans (plus distinctives, via Fontshare ou Google Fonts).

**P9 — Footer visuellement très discret**
Le texte du footer (`text-inkdim` = #A79C8A sur #1A140F) est visuellement très discret, rendant les informations de copyright et les liens sociaux difficiles à lire. Le texte "VISIO IA" est encore plus atténué (`text-inkdim/60`). Le ratio de contraste exact est à valider avec un outil dédié (WebAIM Contrast Checker), mais il est visuellement en dessous des standards d'accessibilité.
**Correction :** Éclaircir le texte du footer et rendre "VISIO IA" cliquable ou le supprimer.

**P10 — Texte développeur visible**
Des commentaires `<!-- EDIT: -->` sont dans le HTML (ils n'apparaissent pas visuellement mais indiquent un travail inachevé), et le texte d'instruction du Showreel est visible publiquement.
**Correction :** Supprimer tous les textes d'instruction et commenter proprement le code.

**P11 — Aucune animation au scroll**
Le site utilise `scroll-behavior:smooth` mais n'a aucune animation déclenchée au scroll (reveal, parallax, sticky scroll). Les sections apparaissent brutalement. Pour un portfolio premium, c'est un manque significatif.
**Correction :** Ajouter Intersection Observer pour des reveals au scroll, parallax subtil sur les images, et transitions entre sections.

**P12 — Formulaire de contact via mailto:**
Le formulaire génère un `mailto:` au lieu d'utiliser un backend ou un service (Formspree, Netlify Forms). Cela ouvre le client email de l'utilisateur, ce qui casse l'expérience sur mobile et ne fonctionne pas sans client email configuré.
**Correction :** Brancher le formulaire sur un service tiers (Formspree, Web3Forms) ou un backend.

#### Mineures (optimisations)

**P13 — Badge tronqué sur mobile**
"DISPONIBLE POUR BOOKINGS" est tronqué en "DISPONIBLE POUR BOOK" sur écran 375px.
**Correction :** Réduire la taille du texte ou utiliser une abréviation sur mobile.

**P14 — Texte "MALI" tronqué sur mobile**
"MANNEQUIN — BAMAKO, MALI" apparaît comme "MANNEQUIN — BAMAKO, MA I" sur mobile.
**Correction :** Ajuster le tracking ou la taille sur mobile.

**P15 — Pas de favicon**
Aucun favicon déclaré. Le navigateur affiche une icône générique.
**Correction :** Créer un favicon SVG "D.D." sur fond sombre.

**P16 — Pas de version imprimable**
La Comp Card (Fiche Technique) est un format standard de l'industrie du mannequinat (5.5"×8.5"). Rien ne permet de l'imprimer correctement.
**Correction :** Ajouter des `@media print` pour une Comp Card imprimable.

**P17 — "VISIO IA" sans lien**
Le texte "VISIO IA" dans le footer n'est pas un lien et n'a aucune fonctionnalité.
**Correction :** Le rendre cliquable ou le supprimer.

---

## 2. Priorisation des modifications

### Indispensables (à corriger avant tout)

1. **Compiler Tailwind en production** — Supprimer le CDN, générer un CSS statique (P1)
2. **Supprimer le texte développeur visible** dans le Showreel (P2, P10)
3. **Remplir les données de contact réelles** (P5)
4. **Ajouter les vraies photos** Polas face + profil (P4)
5. **Ajouter les vraies photos** du Lookbook (P3)
6. **Intégrer les vidéos** du Showreel (P2)
7. **Ajouter les balises SEO** de base : OG tags, favicon, JSON-LD (P7)

### Importantes (à corriger en priorité 2)

8. **Connecter le lightbox** aux vraies images avec navigation (P6)
9. **Remplacer Inter** par une police plus distinctive (P8)
10. **Corriger le contraste** du footer (P9)
11. **Ajouter des animations au scroll** via Intersection Observer (P11)
12. **Brancher le formulaire** sur un service réel (P12)
13. **Corriger les troncatures mobile** badge + MALI (P13, P14)
14. **Ajouter une version imprimable** de la Comp Card (P16)

### Facultatives (optimisations premium)

15. Ajouter un preloader élégant (écran de chargement)
16. Ajouter un curseur personnalisé sur desktop
17. Ajouter un effet de parallax sur les images au scroll
18. Ajouter un son subtil au survol des vidéos (mute par défaut)
19. Ajouter Google Analytics ou Plausible
20. Ajouter une page 404 personnalisée
21. Supprimer ou lier "VISIO IA" (P17)
22. Ajouter des transitions de page (barba.js ou View Transitions API)

---

## 3. Trois concepts de refonte

### Concept A — Dark Cinematic Luxury

**Direction :** Interface sombre, cinématographique, immersive. Le noir profond laisse respirer les photos et vidéos. L'or chaud accentue le luxe. Pensé pour mettre le Showreel au centre.

| Élément | Détail |
|---------|--------|
| **Palette** | Background #0A0908 (noir profond), Surface #15110D, Texte #F5EFE0, Accent or #C6A15B, Accent secondaire #8B6F47 (bronze) |
| **Typographies** | Display : Cormorant Garamond (élégance couture) ou PP Editorial New. Body : Satoshi (moderne, lisible) |
| **Structure** | Hero plein écran avec vidéo de fond → Showreel en section pleine largeur → Comp Card → Galerie immersive → Contact minimaliste |
| **Animations** | Fade-in au scroll, parallax léger sur les images, hover zoom sur les vignettes, transitions de page fluides |
| **Showreel** | Vidéo plein écran en autoplay muet au survol, lecture au clic, overlay minimaliste |
| **Références** | [Isabel Moranta Portfolio](https://www.awwwards.com/sites/isabel-moranta-portfolio) (dark & poetic), [AW Portfolio](https://www.awwwards.com/sites/aw-portfolio) (animation-driven), [Alicja Models](https://www.founderjar.com/inspiration/model-websites/) (dark theme + video) |

**Pourquoi c'est adapté :** Le site actuel utilise déjà une palette sombre + or. Ce concept amplifie cette direction existante vers quelque chose de plus cinématographique et immersif, sans rupture d'identité. C'est le concept le plus proche de l'univers actuel tout en étant nettement plus premium.

---

### Concept B — Minimalisme éditorial

**Direction :** Esthétique épurée inspirée des magazines de luxe (Vogue, Numéro, AnOther). Blanc cassé, noir profond, une seule couleur accent. La typographie fait le spectacle.

| Élément | Détail |
|---------|--------|
| **Palette** | Background #FAF8F5 (blanc cassé), Texte #1A1A1A, Accent #8B6F47 (bronze/tobacco), Séparateurs #E0DDD5 |
| **Typographies** | Display : Playfair Display (magazine) ou Instrument Serif. Body : Work Sans ou General Sans |
| **Structure** | Grille éditoriale stricte, colonnes asymétriques, whitespace généreux. Hero = nom + photo pleine page. Sections numérotées (01, 02, 03...) |
| **Animations** | Minimalistes : fade-in subtil, lignes qui se tracent, images qui se révèlent en clip-path |
| **Showreel** | Encadré dans un cadre éditorial, légende typographique, bouton play minimaliste |
| **Références** | [Gallereee Minimal](https://www.gallereee.com/style/minimal) (minimal portfolios), [Karlie Kloss](https://www.founderjar.com/inspiration/model-websites/) (centralized, black & white), [A1 Gallery Minimal](https://www.a1.gallery/websites/minimal-portfolio) |

**Pourquoi c'est moins adapté :** Le mannequin évolue dans la Haute Couture ET le streetwear. Le minimalisme éditorial pur convient parfaitement à la couture mais peut paraître trop froid pour le côté streetwear/sportswear. De plus, l'identité actuelle est sombre ; un basculement vers le clair serait une rupture majeure.

---

### Concept C — Creative Digital Experience

**Direction :** Audacieux, typographie expressive XXL, compositions asymétriques, interactions créatives. Le site devient une expérience interactive, pas juste un book.

| Élément | Détail |
|---------|--------|
| **Palette** | Background #0D0D0D, Texte #FFFFFF, Accent #FF4D00 (orange vif) ou #00FF88 (vert électrique), Secondaire #1A1A1A |
| **Typographies** | Display : Clash Display ou Migra (expressif, géométrique). Body : Satoshi ou Inter Tight. Monospace pour les datas : JetBrains Mono |
| **Structure** | Sections full-viewport, scroll horizontal pour le lookbook, texte qui se transforme au scroll, grille brisée |
| **Animations** | Texte qui se déchire/transforme, images en morphing, scroll-triggered 3D, cursor magnétique, transitions de page GSAP |
| **Showreel** | Vidéo en background avec texte qui se superpose et réagit au mouvement de souris |
| **Références** | [Awwwards Portfolio Winners](https://www.awwwards.com/websites/portfolio/) (animation-driven), [Motion Folios](https://motionfolios.com/) (creative motion), [A1 Animated](https://www.a1.gallery/websites/animated-portfolio) (page transitions) |

**Pourquoi c'est moins adapté :** Ce concept est spectaculaire mais risque d'éclipser le sujet (le mannequin) au profit de l'effet technique. Pour un book mannequin, la simplicité et la lisibilité des photos doivent primer. Ce concept conviendrait mieux à un motion designer ou un studio créatif.

---

### Recommandation finale

**Le Concept A — Dark Cinematic Luxury est le plus adapté.**

Justification :
1. **Continuité d'identité** : Le site utilise déjà une palette sombre + or. Ce concept amplifie cette direction existante.
2. **Valorisation du Showreel** : Le mannequin a besoin de montrer son runway walk et sa présentation. Un format sombre et cinématographique met la vidéo au centre.
3. **Polyvalence** : Le dark cinematic luxury fonctionne aussi bien pour la Haute Couture que pour le streetwear.
4. **Accessibilité** : Un fond sombre avec texte clair offre un bon contraste naturel, plus facile à maintenir que le blanc éditorial.
5. **Performance** : Moins d'effets JS lourds que le Concept C, plus facile à optimiser.
6. **Références validées** : Isabel Moranta (Awwwards SOTD) et AW Portfolio (Awwwards SOTD) démontrent que cette direction est reconnue dans l'industrie.

Nous utiliserons donc le Concept A comme base, en intégrant des éléments du Concept B (rigueur typographique éditoriale, whitespace) pour tempérer le côté purement technique.

---

## 4. Wireframe détaillé de la nouvelle page d'accueil

```
┌─────────────────────────────────────────────────────────────┐
│  [D.D.]     FICHE TECHNIQUE  POLAS  LOOKBOOK  SHOWREEL  CONTACT     [FR|EN]  [WhatsApp]
├─────────────────────────────────────────────────────────────┤
│  HEADER (fixed, backdrop-blur, transparent → bg/80 au scroll) │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│   ┌──────────────────────────────────────────────────────┐  │
│   │                                                      │  │
│   │              [VIDÉO HERO EN AUTOPLAY MUTED]           │  │
│   │              ou IMAGE PLEIN ÉCRAN (object-cover)      │  │
│   │                                                      │  │
│   │   ● DISPONIBLE POUR BOOKINGS          1,95M          │  │
│   │                                                      │  │
│   │   MANNEQUIN — BAMAKO, MALI                           │  │
│   │                                                      │  │
│   │   Doudou                                             │  │
│   │   Diakite                                             │  │
│   │                                                      │  │
│   └──────────────────────────────────────────────────────┘  │
│                                                              │
│   ────────────────────────────────────────────────────────   │
│   À PROPOS    Formé à la gymnastique et au basketball...     │
│              1,95m d'allure, un regard précis...              │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  01 — FICHE TECHNIQUE                        MISE À JOUR 2026│
│                                                              │
│   ┌──────────────┐   ┌────────────────────────────────────┐ │
│   │              │   │ Taille              1,95m / 6'5"   │ │
│   │   PHOTO      │   │ Tour de poitrine    98cm / 39"     │ │
│   │   PORTRAIT   │   │ Tour de taille      80cm / 31"     │ │
│   │   3:4        │   │ Tour de hanches     96cm / 38"     │ │
│   │              │   │ Pointure            45 EU / 11 US  │ │
│   │              │   │ Yeux                Marron foncé   │ │
│   │              │   │ Cheveux             Noir, court    │ │
│   │              │   │ Disciplines         Gym · Basket    │ │
│   │              │   │ Univers             Couture · SW   │ │
│   │              │   │ Basé à              Bamako, Mali   │ │
│   └──────────────┘   └────────────────────────────────────┘ │
│                                                              │
│   [TÉLÉCHARGER COMP CARD PDF]                                │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  02 — POLAS                              SCOUTING           │
│   Lumière naturelle, sans retouche, sans pose.              │
│                                                              │
│   ┌──────────┐  ┌──────────┐  ┌──────────┐                  │
│   │  FACE /  │  │ PROFIL / │  │  PLEIN   │                  │
│   │  FRONT   │  │ PROFILE  │  │  PIED    │                  │
│   │  3:4     │  │  3:4     │  │  3:4     │                  │
│   └──────────┘  └──────────┘  └──────────┘                  │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  03 — LOOKBOOK                            [TOUT] [ATH] [SW] [HC]│
│                                                              │
│   ┌────────┐ ┌────────┐ ┌────────┐                         │
│   │ IMG 01 │ │ IMG 02 │ │ IMG 03 │  ← hover: zoom + caption│
│   │ 4:5    │ │ 4:5    │ │ 4:5    │    click: ouvre lightbox│
│   └────────┘ └────────┘ └────────┘                         │
│   ┌────────┐ ┌────────┐ ┌────────┐                         │
│   │ IMG 04 │ │ IMG 05 │ │ IMG 06 │                         │
│   │ 4:5    │ │ 4:5    │ │ 4:5    │                         │
│   └────────┘ └────────┘ └────────┘                         │
│   ┌────────┐ ┌────────┐ ┌────────┐                         │
│   │ IMG 07 │ │ IMG 08 │ │ IMG 09 │                         │
│   │ 4:5    │ │ 4:5    │ │ 4:5    │                         │
│   └────────┘ └────────┘ └────────┘                         │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  04 — SHOWREEL                            MOUVEMENT          │
│                                                              │
│   ┌──────────────────────────────────────────────────────┐  │
│   │                                                      │  │
│   │           [▶ VIDÉO RUNWAY WALK — 16:9]              │  │
│   │           (autoplay muted au survol,                │  │
│   │            lecture au clic)                         │  │
│   │                                                      │  │
│   └──────────────────────────────────────────────────────┘  │
│                                                              │
│   ┌──────────────────────────────────────────────────────┐  │
│   │                                                      │  │
│   │           [▶ VIDÉO PRÉSENTATION — 16:9]             │  │
│   │                                                      │  │
│   └──────────────────────────────────────────────────────┘  │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  05 — CONTACT                            BOOKING            │
│                                                              │
│   ┌────────────────────────┐  ┌───────────────────────────┐│
│   │ Contactez le management │  │ NOM / AGENCE             ││
│   │                        │  │ ________________________  ││
│   │ Manager — [Nom]        │  │                          ││
│   │ Email — booking@...    │  │ EMAIL                    ││
│   │ Tél. — +223 ...        │  │ ________________________  ││
│   │                        │  │                          ││
│   │ [WhatsApp]  [Email]    │  │ MESSAGE                  ││
│   │                        │  │ ________________________  ││
│   │                        │  │ ________________________  ││
│   │                        │  │                          ││
│   │                        │  │      [ENVOYER]           ││
│   └────────────────────────┘  └───────────────────────────┘│
│                                                              │
│   ────────────────────────────────────────────────────────   │
│   © 2026 Doudou Diakite — Tous droits réservés              │
│   Instagram · TikTok                                        │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Wireframe mobile (375px) :**

```
┌─────────────────────┐
│ [D.D.]    [≡]        │  ← Header simplifié, burger menu
├─────────────────────┤
│                     │
│   [PHOTO/VIDÉO      │  ← Hero plein écran
│    PLEIN ÉCRAN]      │     Badge en haut
│                     │
│   ● DISPONIBLE      │
│                     │
│   MANNEQUIN —        │
│   BAMAKO, MALI      │
│                     │
│   Doudou             │
│   Diakite            │
│                     │
├─────────────────────┤
│ À PROPOS            │
│ Formé à la...        │
├─────────────────────┤
│ 01 — FICHE          │
│ TECHNIQUE           │
│                     │
│ [PHOTO]             │
│ Taille: 1,95m       │
│ Poitrine: 98cm      │
│ ...                 │
│ [TÉLÉCHARGER PDF]   │
├─────────────────────┤
│ 02 — POLAS          │
│ [IMG] [IMG]         │  ← 2 colonnes sur mobile
│ [IMG]               │
├─────────────────────┤
│ 03 — LOOKBOOK       │
│ [TOUT][ATH][SW][HC] │
│ [IMG] [IMG]         │  ← 2 colonnes sur mobile
│ [IMG] [IMG]         │
│ [IMG] [IMG]         │
│ [IMG] [IMG]         │
│ [IMG]               │
├─────────────────────┤
│ 04 — SHOWREEL       │
│ [▶ VIDÉO 16:9]      │  ← Pleine largeur
│ [▶ VIDÉO 16:9]      │
├─────────────────────┤
│ 05 — CONTACT        │
│ Manager — [Nom]     │
│ Email — booking@... │
│ Tél. — +223 ...     │
│ [WhatsApp] [Email]  │
│                     │
│ NOM / AGENCE        │
│ ________________    │
│ EMAIL               │
│ ________________    │
│ MESSAGE             │
│ ________________    │
│ [ENVOYER]           │
├─────────────────────┤
│ © 2026 Doudou       │
│ Instagram · TikTok  │
└─────────────────────┘
```

---

## 5. Plan de refonte section par section

### 5.1 Header et navigation

**Disposition :**
- Header fixed, hauteur 64px, `backdrop-blur-md` avec `background: rgba(10,9,8,0.8)`
- Logo "D.D." à gauche (Fraunces, 18px, tracking 0.14em)
- Navigation centrée (desktop ≥1024px) : 5 liens, 11px, tracking 0.16em, couleur inkdim → gold au hover
- À droite : switch FR/EN (pill) + bouton WhatsApp (gold, rounded-full)
- Mobile : burger menu (rounded-full, border hair), menu déroulant plein écran

**Améliorations :**
- Ajouter un effet de scroll : header transparent en haut → bg/80 + border-bottom au scroll (Intersection Observer)
- Ajouter un indicateur de section active dans la nav (surligner le lien de la section visible)
- Burger menu : animation d'ouverture en slide-down + croix qui se dessine
- Ajouter un progress bar de scroll (1px, gold, en bas du header)

**Mobile :**
- Navigation simplifiée : logo + burger uniquement (supprimer FR/EN et WhatsApp du header, les déplacer dans le menu ouvert)
- Menu mobile plein écran avec grands liens (16px, espacés)

### 5.2 Hero section

**Disposition :**
- Plein écran (100vh) sur desktop, 85vh sur mobile
- Image ou vidéo de fond en `object-cover`, `position: absolute, inset: 0`
- Overlay gradient sombre en bas (linear-gradient transparent → #0A0908)
- Badge "Disponible" en haut à droite (gold border, backdrop-blur, pulse vert)
- Nom en bas à gauche : "MANNEQUIN — BAMAKO, MALI" (gold, 11px) + "Doudou Diakite" (Fraunces, clamp(38px, 7vw, 72px), font-light, leading 0.88)
- Ruler SVG vertical à droite (desktop uniquement, hidden sur mobile)

**Dimensions :**
- Image hero : 1080×1722 (déjà présente, bon format)
- Min-height : 520px sur mobile, 100vh sur desktop

**Animations :**
- Image avec `scale(1.05)` au chargement → `scale(1)` en 1.2s (effet Ken Burns subtil)
- Texte en fade-in + translateY (déjà présent via .rise)
- Badge en fade-in différé (rise-2)

**Mobile :**
- Badge : "DISPONIBLE" au lieu de "DISPONIBLE POUR BOOKINGS" (text-[9px])
- "MANNEQUIN — BAMAKO, MALI" en text-[10px] pour éviter la troncature
- Ruler supprimé (hidden)
- Bio en dessous, dans une grille simple (1 colonne)

### 5.3 Showreel

**Disposition :**
- Section pleine largeur (max-w-6xl), padding généreux (py-14)
- Titre "Showreel" + label "MOUVEMENT" au-dessus
- Deux vidéos côte à côte (grid lg:grid-cols-2, gap-5)
- Chaque vidéo : aspect-ratio 16/9, rounded-lg, overflow-hidden

**Recommandations pour rendre le Showreel spectaculaire sans ralentir :**

1. **Utiliser des vidéos YouTube/Vimeo en embed** plutôt que des fichiers hébergés (économie de bande passante, lecteur optimisé)
2. **Thumbnail image** : afficher une image poster (poster attribute) au lieu de charger la vidéo immédiatement. Chargement paresseux (loading="lazy") avec `preload="none"`
3. **Lecture au survol (desktop)** : autoplay muted au hover, pause au leave. Utiliser `iframe` avec paramètre `autoplay=1&mute=1` injecté au hover
4. **Bouton play** : cercle blanc avec icône triangle, scale(1.06) au hover (déjà présent)
5. **Label vidéo** : badge en bas à gauche avec backdrop-blur (déjà présent)
6. **Ne pas utiliser de background video** pour le hero (trop lourd). Préférer une image statique avec option de lecture vidéo au clic
7. **Format WebM** pour les vidéos auto-hébergées (meilleure compression que MP4)
8. **Taille des vidéos** : max 720p pour le streaming (1080p seulement si full-screen)

### 5.4 Galerie des projets (Lookbook)

**Disposition :**
- Grille 3 colonnes desktop, 2 colonnes mobile
- Filtres en haut à droite : TOUT, ATHLETIC, STREETWEAR, HAUTE COUTURE
- Chaque item : aspect-ratio 4/5, rounded-lg, overflow-hidden
- Hover : scale(1.03) sur l'image + overlay sombre + caption qui apparaît

**Améliorations :**
- Ajouter `loading="lazy"` sur toutes les images
- Ajouter une transition de filtre fluide (opacity → display none, pas de saut de layout)
- Lightbox : image agrandie + navigation précédent/suivant + compteur (3/9)
- Ajouter `srcset` pour servir des images optimisées par taille d'écran

### 5.5 Services et compétences

Cette section n'existe pas actuellement mais devrait être ajoutée pour un portfolio complet de mannequin :

**Disposition :**
- Section entre le Lookbook et le Showreel
- Grille 3 colonnes : Haute Couture | Streetwear | Sportswear
- Chaque colonne : icône + titre + description courte + 2-3 photos miniatures

**Contenu suggéré :**
- Haute Couture : Défilés, shootings éditoriaux, campagnes luxe
- Streetwear : Collaborations marques urbaines, lookbooks, campagnes street
- Sportswear : Campagnes sport, mouvements athlétiques, fitness

### 5.6 À propos

**Disposition :**
- Section dédiée avec photo + texte
- Grille 2 colonnes : photo 3:4 à gauche, texte à droite
- Bio enrichie : parcours, disciplines, disponibilités, références

**Améliorations :**
- Ajouter des "chiffres clés" : 1,95m, 45 EU, 3 univers, disponible international
- Ajouter des logos de marques/clients (si disponibles)
- Lien vers Instagram/TikTok avec aperçu feed

### 5.7 Contact et footer

**Disposition :**
- Section sur fond bgalt (déjà présent)
- Grille 2 colonnes : infos à gauche, formulaire à droite
- Footer en bas : copyright + réseaux sociaux

**Améliorations :**
- Remplacer les placeholders par les vraies coordonnées
- Brancher le formulaire sur Formspree/Web3Forms (au lieu de mailto:)
- Ajouter un lien de téléchargement de la Comp Card PDF
- Footer : contraste amélioré, liens cliquables, suppression de "VISIO IA" ou lien réel
- Ajouter un lien "Retour en haut" avec flèche

---

## 6. Références de portfolios premium

| # | Nom | Source | Points forts adaptables |
|---|-----|--------|--------------------------|
| 1 | **Isabel Moranta** | [Awwwards SOTD](https://www.awwwards.com/sites/isabel-moranta-portfolio) | Dark & poetic, célébration du parcours, transitions fluides entre projets |
| 2 | **AW Portfolio** | [Awwwards SOTD](https://www.awwwards.com/sites/aw-portfolio) | Animation-driven, high-impact, UX mémorable |
| 3 | **Romain Vincens** | [Awwwards Inspiration](https://www.awwwards.com/inspiration/dark-theme-romain-vincens-portfolio) | Dark theme maîtrisé, typographie expressive, parallax |
| 4 | **Alicja Models** | [FounderJar — 19 Best Model Websites](https://www.founderjar.com/inspiration/model-websites/) | Dark theme one-page, vidéo full-width, monochrome maîtrisé |
| 5 | **Karlie Kloss** | [FounderJar — 19 Best Model Websites](https://www.founderjar.com/inspiration/model-websites/) | Layout centralisé, noir & blanc, social icons latéraux |
| 6 | **Kristina Smolyar** | [FounderJar — 19 Best Model Websites](https://www.founderjar.com/inspiration/model-websites/) | Hero video centralisé, slideshow de logos de marques |
| 7 | **Chanelle Renee** | [FounderJar — 19 Best Model Websites](https://www.founderjar.com/inspiration/model-websites/) | Hero video avec CTA, sticky header, noir & blanc |
| 8 | **Victoria Togoe** | [FounderJar — 19 Best Model Websites](https://www.founderjar.com/inspiration/model-websites/) | Parallax scrolling, images full-width par catégorie |
| 9 | **Gallereee Minimal** | [gallereee.com/style/minimal](https://www.gallereee.com/style/minimal) | Minimalisme éditorial, whitespace, "less but done really well" |
| 10 | **Gallereee Dark** | [gallereee.com/style/dark-mode](https://www.gallereee.com/style/dark-mode) | Dark mode maîtrisé, contraste et profondeur |

> **Note :** Les références ci-dessus sont issues d'articles de curation (Awwwards, FounderJar, Gallereee). Les URLs live directs des portfolios individuels n'ont pas tous pu être extraits depuis ces pages. Visitez les articles sources pour accéder aux sites live de chaque créateur.

**Principes adaptables extraits :**
- Hero plein écran avec image ou vidéo de haute qualité
- Navigation sticky/fixed avec backdrop-blur
- Animations au scroll (fade-in, parallax) mais discrètes
- Noir & blanc ou palette sombre + or pour le luxe
- Typographie serif pour les titres (caractère éditorial)
- Whitespace généreux entre les sections
- Lightbox pour la galerie avec navigation
- Vidéo en hero ou en section dédiée avec autoplay muted au survol

---

## 7. Recommandations techniques

### 7.1 Architecture

Le site actuel est un seul fichier HTML avec Tailwind CDN. Pour la refonte :

**Option recommandée : HTML statique + Tailwind compilé**
- Conserver la structure single-page (le site est court, pas besoin de routing)
- Compiler Tailwind via CLI : `npx tailwindcss -i input.css -o output.css --minify`
- Séparer le CSS et le JS dans des fichiers externes
- Utiliser un build simple (Vite en mode statique) si vous prévoyez d'ajouter du JS complexe

**Alternative : Framer ou Webflow**
- Si vous ne voulez pas coder, Framer offre des animations natives et un CMS
- Webflow permet un design visuel avec export de code propre

### 7.2 Performance

| Action | Impact estimé |
|--------|---------------|
| Remplacer Tailwind CDN par CSS compilé | -300KB JS, -300ms TTI |
| Ajouter loading="lazy" sur toutes les images | -200KB de transfert initial |
| Compresser les images en WebP/AVIF | -40% du poids des images |
| Ajouter un preconnect pour les fonts | -100ms au font loading |
| Utiliser font-display: swap (déjà présent) | Bon, à conserver |
| Minifier le HTML/CSS/JS | -20% du poids |
| Activer la compression gzip/brotli sur le serveur | -70% du transfert |

### 7.3 Accessibilité

| Action | Impact |
|--------|--------|
| Corriger le contraste du footer | Conformité WCAG AA |
| Ajouter des alt text descriptifs sur toutes les images | Lecteurs d'écran |
| Ajouter des aria-labels sur les boutons sans texte | Navigation assistive |
| S'assurer que le lightbox est navigable au clavier (flèches, Escape) | Accessibilité clavier |
| Ajouter un skip link "Aller au contenu" | Navigation clavier |
| Respecter prefers-reduced-motion (déjà présent) | À conserver |

### 7.4 SEO

| Action | Impact |
|--------|--------|
| Ajouter Open Graph tags + Twitter Cards | Partage social |
| Ajouter JSON-LD Person (nom, métier, photo, taille) | Rich snippets Google |
| Ajouter un sitemap.xml | Indexation |
| Ajouter un robots.txt | Contrôle du crawl |
| Ajouter un favicon (SVG + PNG) | Branding navigateur |
| Optimiser le title et la meta description | CTR dans les résultats |
| Ajouter des hreflang pour FR/EN | Internationalisation |

---

## 8. Proposition de code

Une proposition de code HTML/CSS/JS est fournie dans le fichier `refonte_portfolio.html` joint à cet audit. Il s'agit d'un **prototype de démonstration autonome** (il utilise encore le CDN Tailwind pour faciliter le test — à remplacer par un build compilé en production). Elle implémente le Concept A (Dark Cinematic Luxury) avec :

- Tailwind CSS compilé (à remplacer par votre build)
- Animations au scroll via Intersection Observer
- Lightbox fonctionnel avec navigation
- Showreel avec support vidéo (YouTube embed ou `<video>`)
- Formulaire de contact prêt pour Formspree
- SEO de base (OG tags, JSON-LD, favicon)
- Responsive mobile-first
- Accessibilité améliorée (skip link, aria-labels, contraste)
- Bilingue FR/EN conservé
- Section services ajoutée
- Comp Card téléchargeable
- Footer corrigé

**Note importante :** Ce code est une base de refonte. Il doit être adapté avec vos vraies photos, vidéos, coordonnées et contenu. Les placeholders sont clairement marqués avec des commentaires `<!-- REMPLACER PAR -->`.

---

*Audit réalisé par Perplexity Computer — 13 septembre 2026*
