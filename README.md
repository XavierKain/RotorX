# VentX 🌬️

**Soufflerie de dune interactive pour le soaring en parapente / parakite.**

Simulateur pédagogique qui visualise l'écoulement du vent sur une dune :
compression et accélération sur la face au vent, déviation du flux,
rotor sous le vent (avec vrille hélicoïdale en vent de biais), et effet
venturi dans un passage (type chemin piéton coupant la dune).

Pensé pour mieux comprendre l'aérologie du soaring côtier (Tarifa, Dune du Pilat,
etc.) avec des ailes type Flare Moustache / Bandit.

> ⚠️ Modèle **heuristique pédagogique**, pas une CFD. Les comportements et
> ordres de grandeur sont réalistes, mais ne jamais s'en servir pour décider
> d'un vol limite.

## Démarrage

Aucun build, aucune dépendance à installer :

```bash
# Ouvrir directement dans un navigateur
open index.html

# Ou servir en local (recommandé)
npx serve .
# ou
python3 -m http.server 8080
```

Three.js r128 est chargé depuis cdnjs (connexion internet requise au premier chargement).

## Fichiers

| Fichier | Description |
|---|---|
| `index.html` | **Version courante** — 3D (Three.js), 3 modes de visualisation, vitesse d'animation découplée du vent |
| `legacy/simulateur-3d-v1.html` | 3D v1 — traînées courtes, animation proportionnelle au vent |
| `legacy/simulateur-2d.html` | 2D Canvas — coupe verticale, mode sculpture du terrain au doigt, sonde de vent, overlay zones portance/rotor |
| `docs/PHYSIQUE.md` | Détail du modèle d'écoulement, formules, hypothèses, limites |
| `CLAUDE.md` | Contexte et conventions pour Claude Code |
| `ROADMAP.md` | Idées et prochaines étapes priorisées |

## Fonctionnalités (index.html)

**Paramètres**
- Vent météo : 8–40 kt
- Angle du vent : −60° à +60° (0° = perpendiculaire à la dune)
- Hauteur de dune : 8–55 m
- Pente au vent : 8–35° / Pente sous le vent : 6–45°
- Irrégularités du relief : 0–100 %
- Passage dans la dune : largeur 0–40 m, position −70 à +70 m

**Visualisation**
- 3 modes : *Filets longs* (traînées ~30–50 m), *Lignes de courant*
  (trajectoires complètes figées), *Particules* (rendu court)
- Vitesse d'animation 0–200 %, **indépendante du vent réel** — la couleur
  encode la vitesse réelle (bleu → vert → jaune → rouge), le violet la turbulence
- Caméra orbitale (drag) + zoom (pinch / molette)
- Densité de filets réglable

**Phénomènes simulés**
- Compression / accélération à la crête (soaring)
- Décollement au-delà de ~16° de pente sous le vent → bulle de recirculation
- Rotor : plongée derrière la crête, retour vers la dune au ras du sol
- Vent de biais → rotor hélicoïdal dérivant le long de la crête, ascendance
  réduite (seule la composante perpendiculaire porte)
- Passage : venturi au ras du sol, trou d'ascendance au-dessus de la trouée,
  turbulence des épaulements
- Chaque bosse raide (irrégularités) génère son propre mini-rotor

## Stack

- HTML/CSS/JS vanilla, un seul fichier par version
- Three.js r128 (CDN cdnjs) pour la 3D
- Canvas 2D pour la version legacy 2D
- Aucun framework, aucun bundler — volontairement simple à héberger
  (n'importe quel hébergement statique, y compris un sous-dossier WordPress)

## Auteur

Xavier Kain — [XKConsulting](https://malt.fr/profile/xavierkain) · Tarifa 🪁
Fait partie de la famille d'outils SoarX / GlideX.
