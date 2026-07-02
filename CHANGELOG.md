# CHANGELOG

## v0.3 — calques d'analyse (courant)
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
