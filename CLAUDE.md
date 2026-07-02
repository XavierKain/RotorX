# CLAUDE.md — VentX

Contexte pour Claude Code. Lis aussi `docs/PHYSIQUE.md` avant de toucher au champ de vent.

## Le projet en une phrase

Simulateur pédagogique 3D (Three.js) de l'écoulement du vent sur une dune, pour
comprendre le soaring en parapente/parakite : compression au vent, rotor sous le
vent, vent de biais, venturi dans les passages.

## Utilisateur / contexte

- Xavier, développeur freelance et pilote parakite (Flare Moustache 18m) à Tarifa.
- Usage : pédagogie personnelle + potentiellement partage avec la communauté
  soaring (contenu Instagram, embed sur un site WordPress).
- Langue de l'UI : **français**. Commentaires code : français accepté.

## Architecture actuelle

- **Un seul fichier autonome par version** : `index.html` contient CSS + HTML + JS.
  C'est un choix volontaire (zéro build, hébergement statique trivial, partage facile).
  Ne pas introduire de bundler/framework sans demande explicite. Si le fichier
  devient ingérable (>1500 lignes), proposer un découpage en modules ES chargés
  sans build (`<script type="module">` + fichiers .js à côté) plutôt qu'un tooling.
- `legacy/` : versions précédentes (2D Canvas et 3D v1), à conserver comme référence.
- Three.js **r128 via cdnjs** (seule version dispo sur cdnjs ; OrbitControls absent
  → contrôles caméra custom déjà implémentés, ne pas essayer d'importer
  three/examples).

## Structure du code (index.html)

1. **Terrain** : grille `hg` 128×80 sur un domaine 300×200 m, sol de base G=4 m.
   `buildTerrain()` paramétrique (hauteur, pentes, irrégularités, passage/trouée),
   `hxy(x,y)` échantillonnage bilinéaire.
2. **Champ de vent** : `flow(x,y,z)` → `{u,v,w,turb}` en m/s. Modèle heuristique
   (voir docs/PHYSIQUE.md) : suivi de relief le long de la direction du vent,
   détection de crête au vent par marche arrière, bulle de décollement + vortex
   de rotor, dérive hélicoïdale (composante parallèle à la crête), venturi entre
   parois, déviation latérale vers les points bas.
3. **Rendu Three.js** : mesh terrain vertexColors, filets = `LineSegments` avec
   historique circulaire de K=36 points par particule, lignes de courant =
   second buffer `LineSegments` recalculé à la demande (`buildStreams()`).
   Calques d'analyse (v0.3) : volume rotor + nappe d'ascendance = "canopées"
   96×60 (grille fixe, seule la hauteur bouge ; sommets enfoncés sous le
   terrain quand inactifs → cachés par le depth test), coupe verticale = quad
   `DataTexture` 144×72 + flèches `LineSegments`. Reconstructions amorties par
   dirty flags dans la boucle (moduli 9/11/6). La marche de crête est
   factorisée dans `wakeScan()` (résultat dans l'objet global réutilisé `WK`,
   zéro allocation) — partagée entre `flow()` et `buildRotor()`.
4. **Animation** : vitesse visuelle découplée du vent réel (normalisation par U),
   curseur 0–200 %. La couleur encode la vitesse réelle, le violet la turbulence.
5. **Contrôles** : caméra orbitale custom (pointer events, pinch, molette),
   sliders bindés via `bind()`.

## Conventions

- Unités SI en interne (m, m/s), affichage en kt (`KT=0.51444`).
- Repère sim : x = largeur (vent ~+x), y = profondeur, z = altitude.
  Repère Three : (x, z_sim→y_three, y_sim→z_three). Attention aux conversions.
- Palette : fond `#0B1220`, accent `#5BC8E2`, sable `#6E4F2C→#E0B173`,
  rampe vitesse `#4aa8ff → #7fe08b → #ffd24a → #ff5252`, turbulence `#c86bff`.
- Perf cible : 60 fps sur mobile récent avec ~420 filets × 35 segments.
  Toute feature doit rester fluide sur iPhone. Profiler avant d'augmenter
  les budgets (segments, appels à `flow()` par frame).
- Toujours garder le disclaimer "modèle pédagogique, pas une CFD" visible.

## Pièges connus

- `flow()` fait ~28 échantillonnages terrain par appel (marche de crête incluse).
  C'est le hot path : rester en Float32Array, pas d'allocations dans la boucle.
- Le rotor n'apparaît que si pente sous le vent > ~16° ET chute > 3 m — c'est
  voulu (écoulement attaché sur pente douce).
- Les lignes de courant peuvent spiraler indéfiniment dans le rotor (champ non
  strictement solénoïdal) — limité à 250 pas, c'est assumé et illustratif.
- `LineBasicMaterial` : épaisseur de trait non contrôlable (limite WebGL). Pour
  des traits épais → passer à des ribbons (TriangleStrip) ou Line2 (à vendored,
  pas sur cdnjs).

## Priorités si on te demande "continue"

Voir ROADMAP.md. Ordre suggéré : sonde 3D cliquable → plan de coupe vertical →
ribbons épais → presets de spots réels (Tarifa/Pilat) → import de profil réel.
