# PHYSIQUE.md — Modèle d'écoulement de VentX

Modèle **heuristique** conçu pour reproduire qualitativement les phénomènes
utiles au soaring, à coût CPU compatible temps réel navigateur/mobile.
Ce n'est **pas** une résolution de Navier-Stokes.

## Notation

- Domaine : 300 m (x) × 200 m (y) × ~140 m (z). Sol de base `G = 4 m`.
- `U` : vent météo (m/s). `dir = (cosθ, sinθ)` : direction horizontale du vent
  (θ = 0 → perpendiculaire à la crête, qui court le long de y à x = 140 m).
- `h(x,y)` : altitude du terrain (grille 128×80, interpolation bilinéaire).
- `d` = direction du vent, `p` = perpendiculaire horizontale à `d`.

## 1. Écoulement de base (suivi de relief + compression)

Pour un point (x, y, z), le modèle travaille dans le **plan vertical contenant
la direction du vent** (approximation "en tranches").

- **Terrain effectif** `he` : le terrain réel, ou l'enveloppe de la bulle de
  décollement si le point est dans un sillage (voir §2).
- **Décroissance verticale** : `f = exp(−(z − he)/D)` avec `D = max(18, 1.6·H)`.
  L'influence du relief s'estompe avec l'altitude.
- **Compression / speed-up** (blocage par le relief) :
  `q = U · (1 + 0.9 · (he − G)/40 · f)`
  → jusqu'à ~2× U à la crête d'une dune de 50 m, cohérent avec les speed-up
  observés en soaring.
- **Composante verticale** (suivi de pente) :
  `w = q · ∂he/∂s · f · 0.9` où s est l'abscisse le long de `d`.
- **Déviation latérale** : le flux est dévié vers les points bas :
  `v_lat = −1.0 · q · f · ∂h/∂p`, plafonné à 0.8·U.
  C'est ce qui fait converger l'air vers un passage en amont, et contourner
  les extrémités/épaulements.

## 2. Décollement et rotor

- **Détection de crête au vent** : marche arrière le long de −d (20 pas de
  5.5 m). On cherche la première montée raide (pente > 0.30 ≈ 17°) et on suit
  jusqu'au sommet local → crête de hauteur `hc` à distance `sC`.
- **Critère de sillage** : chute `drop = hc − h > 3 m` ET point situé à moins de
  `wakeLen = max(22, 6·drop)` de la crête (règle empirique : recollement à
  ~5–7 × la hauteur de chute).
- **Enveloppe de la bulle** : `env(t) = h + drop·(1 − t)^1.6` avec
  `t = sC/wakeLen`. L'écoulement extérieur "surfe" sur cette enveloppe
  (d'où la plongée du flux derrière la crête).
- **Vortex de rotor** (sous l'enveloppe) : rotation dans le plan du vent,
  centre à `t ≈ 0.4`, `zc = h + 0.45·drop`, vitesse tangentielle ~0.6·U
  plafonnée à 1.15·U. Sens de rotation : flux aval en haut de bulle, plongée
  au recollement, **retour vers la dune au ras du sol**, remontée de la pente
  sous le vent.
- **Fondu** `m` : profondeur dans la bulle × fenêtres lisses en début/fin de
  sillage. `turb = m` alimente le jitter aléatoire (±0.8·U·turb par composante)
  et la teinte violette.
- **Dérive hélicoïdale** (vent de biais) : à l'intérieur du rotor, on réinjecte
  65 % de la composante du vent **parallèle à la crête locale** (tangente =
  perpendiculaire au gradient du terrain). Résultat : le rotor est un tourbillon
  d'axe ~parallèle à la crête qui translate le long de celle-ci → hélice.

Conséquence pédagogique correcte : pente sous le vent < ~16° → pas de
décollement, écoulement attaché, pas de rotor.

## 3. Venturi (passage / trouée)

Effet de canalisation entre parois :
`wall = max(0, moyenne(h à ±12 m perpendiculaire) − h)`
Si `wall > 2 m` : `q *= 1 + min(0.9, 0.05·wall) · exp(−(z−h)/max(8, wall))`
→ accélération au ras du sol dans le couloir, s'estompant en hauteur et
disparaissant si le passage s'élargit (constriction relative plus faible).
Les épaulements du passage sont vus par le détecteur de crête comme des
crêtes obliques → mini-rotors de bord (émergent, pas codé en dur).

## 4. Ce que le modèle NE fait PAS

- Pas de conservation stricte de la masse (champ non solénoïdal) — les lignes
  de courant peuvent spiraler dans le rotor sans se refermer exactement.
- Pas de gradient de vent vertical en entrée (U uniforme en altitude).
- Pas de couche limite ni de rugosité, pas d'effets thermiques ni de stabilité
  atmosphérique, pas d'instationnarité réelle (le "battement" des rotors réels).
- La turbulence est un bruit blanc modulé, pas un spectre réaliste.
- Le venturi n'accélère que la composante le long du vent (pas de jet 3D complet).

## 5. Ordres de grandeur attendus (sanity checks)

| Phénomène | Modèle | Réalité terrain |
|---|---|---|
| Speed-up crête (dune 30 m, pente 20°) | ×1.5–1.8 | ×1.3–2 selon profil |
| Longueur de rotor | ~6× hauteur de chute | 4–10× selon stabilité |
| Vitesse dans le rotor | jusqu'à ~1.15× U, inversée au sol | rafales chaotiques du même ordre |
| Angle critique de décollement | ~16–17° | ~15–20° (dépend rugosité/stabilité) |

Si une modification casse un de ces comportements, c'est une régression.
