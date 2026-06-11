# Topo3D 🧊

Sélectionne une zone sur une carte, génère un **modèle 3D du relief**, et télécharge un **fichier STL imprimable en 3D**.

**En ligne :** https://topo3d.tbrun.dev

## Comment ça marche

1. **Carte** ([MapLibre GL](https://maplibre.org/)) — fond OSM ou satellite, sélection de la zone par cliquer-glisser (rectangle).
2. **Élévation** — tuiles de terrain [Terrarium](https://github.com/tilezen/joerd/blob/master/docs/formats.md) (jeu de données *Terrain Tiles* sur AWS Open Data, gratuit, sans clé). Décodées côté navigateur : `altitude = (R·256 + G + B/256) − 32768`.
3. **Maillage** ([Three.js](https://threejs.org/)) — surface du terrain + 4 murs + socle plat = **solide étanche** (manifold), donc directement imprimable.
4. **Export STL** binaire (`STLExporter`), interprété en **millimètres** par les slicers.

## Réglages

- **Taille max** (mm) — plus grande dimension du modèle, ratio de la zone préservé.
- **Exagération verticale** — × l'échelle réelle (le relief est sinon souvent trop plat à petite échelle).
- **Épaisseur du socle** (mm).
- **Résolution** — densité de la grille d'échantillonnage.

## Stack

Application **statique** (un seul `index.html`), libs via CDN (MapLibre, Three.js). Aucune donnée envoyée à un serveur tiers hormis les tuiles.

> Les tuiles d'élévation sont récupérées via un proxy `/dem/*` (configuré côté Caddy) qui relaie vers `s3.amazonaws.com/elevation-tiles-prod/terrarium/{z}/{x}/{y}.png`, afin d'éviter les soucis de CORS et de canvas *tainted*. En dev local, sers le dossier derrière un proxy équivalent (ou pointe directement sur la source S3).

## Déploiement

Statique : `index.html` servi par Caddy (`topo3d.tbrun.dev`) avec le bloc `handle_path /dem/*` pour le proxy d'élévation.

## Crédits données

- Élévation : *Terrain Tiles* (Mapzen / Tilezen / Nextzen) — AWS Open Data.
- Fonds de carte : © OpenStreetMap · © Esri, Maxar, Earthstar Geographics.
