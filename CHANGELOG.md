# CHANGELOG

## v0.2 — 3D, visualisation avancée (courant)
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
