# ROADMAP — VentX

## v0.3 (court terme)

- [ ] **Sonde 3D** : cliquer/toucher un point de l'espace → lecture vitesse (kt),
      Vz (kt ou m/s), cap local, indicateur turbulence. Raycast sur un plan
      vertical aligné au vent passant par le point cliqué au sol.
- [x] **Plan de coupe vertical** déplaçable (slider profondeur) : affiche la
      tranche 2D du champ (comme la version legacy 2D) en overlay dans la scène
      3D — le meilleur des deux mondes. *(v0.3)*
- [ ] **Pause + pas-à-pas** en complément du curseur de vitesse (bouton ⏸ / ▶ / ⏭).
- [ ] Presets rapides : "Dune école", "Falaise", "Dune du Pilat", "Balnéario Tarifa"
      (hauteur/pentes/vent typiques pré-remplis).

## v0.4 (visualisation)

- [ ] **Ribbons épais** à la place des lignes 1px : TriangleStrip orienté caméra,
      largeur ~0.6 m, fondu alpha réel sur la queue. Gros gain de lisibilité.
- [x] Iso-surface ou nuage semi-transparent de la zone de turbulence (rotor
      visible comme un "tube" violet). *(v0.3 — enveloppe analytique)*
- [x] Surface d'ascendance : iso Vz > seuil réglable (ex. taux de chute de la
      Moustache 18 ≈ 1.1 m/s) → visualiser directement "où ça tient". *(v0.3)*
- [ ] Mode "vue pilote" : caméra placée dans la zone de soaring, regard vers la dune.

## v0.5 (terrain réel)

- [ ] Import d'un profil de dune réel : dessin 2D extrudé (reprendre le mode
      sculpture de la version legacy 2D, l'étendre en 3D par sections).
- [ ] Import heightmap (image PNG niveau de gris ou données IGN/opentopodata)
      pour simuler un spot réel.
- [ ] Plusieurs passages / brèches simultanés.

## v1.0 (partage)

- [ ] Partage d'un état via URL (params sérialisés dans le hash).
- [ ] Export vidéo/GIF d'une séquence (pour Instagram — captures orbitales
      automatiques autour du rotor).
- [ ] Page d'accueil avec explications aérologie illustrées (compression,
      gradient, rotor, venturi) + intégration WordPress (iframe ou bloc).
- [ ] i18n FR/EN/ES.

## Idées en vrac (non triées)

- Gradient de vent vertical réaliste (profil log) en entrée de domaine.
- Thermique simple en complément du dynamique (colonne ascendante paramétrable).
- Mode comparaison côte à côte (2 jeux de paramètres).
- Trajectoire d'une aile simulée (polaire Moustache 18) en surimpression :
  "est-ce que ça tient ?" — vitesse air vs vitesse sol vs zone de portance.
- Son : intensité du vent audio-réactive (immersion).

## Anti-objectifs

- Pas de vraie CFD (Navier-Stokes) : hors budget perf navigateur/mobile,
  et hors scope pédagogique.
- Pas de backend : tout doit rester statique.
- Pas de framework front sans nécessité démontrée.
