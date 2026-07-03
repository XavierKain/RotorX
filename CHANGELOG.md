# CHANGELOG

## v0.6 — lecture pilote (courant)
Inspiré de l'article spots.guru « How Numerical Simulation Revolutionized My
Soaring Practice » (décomposition u/v du champ, lecture du point de vue pilote).
- **Champ de la coupe** sélectionnable :
  - *Vitesse* (LIC teinté vitesse, comme avant)
  - *Monte / descend* : Vz net = Vz air − taux de chute de l'aile —
    rouge/orange/vert = ça monte, gris = zérotage, bleu = ça descend
  - *Avance / recul* : vent horizontal vs vitesse de l'aile (nouveau slider
    « Vitesse de l'aile », 12–30 kt) — vert = tu avances, jaune = sur place,
    orange/rouge = tu recules
- **Gradient vertical de vent** (toggle, off par défaut) : profil log
  (z0 = 5 cm, référence à 10 m au-dessus du relief effectif, plafonné ×1.35)
  → montre le vent plus faible au ras du sol (décoller/atterrir bas)

## v0.5 — visualisation "état de l'art"
- **Lignes également espacées** (algo type Jobard–Lefer) : semis à ~9 m,
  arrêt avec hystérésis à ~2.4 m — le plan se remplit uniformément, la
  spirale du rotor s'arrête après un tour (fini la pelote), intégration
  RK2 avant + arrière
- **Comètes** à la place des flèches : impulsions lumineuses (tête vive,
  traîne amont) avançant en temps de parcours réel — plus d'entassement
  de triangles au centre du vortex
- **Coupe orientable** : nouvel angle −90°/+90° par rapport au vent
  (90° = plan parallèle à la crête → composante travers + verticale,
  utile en vent de biais)
- **LIC** (Line Integral Convolution) sur le calque coupe : bruit étiré le
  long du champ → le vortex apparaît comme une empreinte digitale ; la
  grille de flèches fines est supprimée

## v0.4 — vue Schéma
- Nouveau mode **Schéma** (défaut) : lisibilité type planche pédagogique,
  mais calculée sur le vrai champ — lignes de courant du plan de coupe
  intégrées en RK2, rendues en **rubans épais** orientés caméra
- **Flèches blanches animées** le long des lignes, espacées en temps de
  parcours : elles se resserrent où l'air ralentit, s'étirent où il accélère,
  et tournent dans le rotor
- Spirale du rotor tracée depuis le centre du vortex (graines à t≈0.4 du
  sillage, 3 rayons) ; les lignes suivent le terrain au lieu de s'y arrêter
  (recirculation au sol visible)
- Vue de profil automatique en entrant dans le mode (« ↺ Vue » y revient)
- Fix : `frustumCulled=false` sur tous les buffers dynamiques (rubans,
  flèches, coupe) — bounding sphere calculée avant le premier remplissage

## v0.3 — calques d'analyse
- **Volume rotor** : enveloppe 3D violette de la bulle de recirculation, maillée
  analytiquement (canopée 96×60, sommets enfoncés sous le terrain hors sillage)
- **Nappe d'ascendance** : surface verte du plafond où Vz ≥ seuil réglable
  (0.3–3 m/s, défaut 1.1 = taux de chute Moustache 18), turbulence exclue
- **Coupe verticale** : plan 2D aligné au vent, déplaçable le long de la crête
  (texture couleur = vitesse, alpha = anomalie, flèches = mouvement dans le plan)
- Refactor : marche de crête factorisée (`wakeScan`) sans changer `flow()`,
  reconstructions amorties dans la boucle (dirty flags + moduli)

## v0.2 — 3D, visualisation avancée
- Vitesse d'animation découplée du vent réel (0–200 %)
- Filets longs : historique de 36 points (~30–50 m de trajectoire), fondu de queue
- Mode "Lignes de courant" : trajectoires complètes recalculées à chaque réglage
- Mode "Particules" (rendu court v1 conservé)
- Densité de filets réglable

## v0.1 — 3D initiale
- Terrain 3D paramétrique (hauteur, pentes, irrégularités)
- Vent de biais −60°/+60°, rotor hélicoïdal avec dérive le long de la crête
- Passage/trouée : venturi, trou d'ascendance, turbulence des épaulements
- Caméra orbitale custom (pointer events, pinch, molette)

## v0.0 — 2D (legacy/simulateur-2d.html)
- Coupe verticale Canvas 2D, sculpture du terrain au doigt
- Sonde de vent (vitesse + Vz), overlay zones portance/descendance/rotor
- Bulles de décollement multiples (une par bosse raide)
