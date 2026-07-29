# Landscape des outils ANS

Site généré avec [landscape2](https://github.com/cncf/landscape2), la même
brique logicielle qui alimente [landscape.cncf.io](https://landscape.cncf.io),
mais utilisée ici dans une version très allégée : **1 à 5 outils maximum par
sous-catégorie**, pour aider à choisir le bon outil plutôt qu'à explorer un
écosystème entier.

## Fichiers

- `data.yml` — catégories, sous-catégories et outils affichés
- `settings.yml` — branding, onglets ("groups"), mode d'affichage
- `guide.yml` — texte explicatif par catégorie (page `/guide`)
- `logos/` — un fichier logo par outil (SVG ou PNG), référencé par son nom de fichier dans `data.yml`

## Contenu actuel : inventaire réel de l'ANS + quelques exemples de marché

`data.yml` contient 157 outils répartis dans 16 catégories. La grande
majorité (~115) provient de l'inventaire applicatif réel de l'ANS
(`MApplication-20260729.xlsx`, export d'une cartographie applicative) :
chaque application y a été rattachée à une catégorie/sous-catégorie, avec
son statut déduit de son usage effectif (`recommande` par défaut pour un
outil en production, `alternative` pour un usage plus ponctuel). Les
instances multiples d'un même produit (ex. Jira ANS/Ségur, Confluence
ANS/Ségur, SharePoint Région/Ségur Numérique, N8N interne/Ségur, Wazuh
DSI/PSC, Squash TM ANS/référencement MES/Ségur) ont été fusionnées en une
seule entrée pour éviter les doublons.

Le reste (~40 outils, essentiellement dans les catégories Développement,
DevOps, IA, Données et APIs) provient d'une itération précédente basée sur
des outils de marché illustratifs — **à confirmer ou retirer** selon la
politique effective de l'agence.

Les logos suivent une cascade à 3 niveaux (voir section suivante) :
1. **simple-icons** (vecteur, recoloré à la couleur de marque officielle) quand l'outil y est référencé — la majorité des outils "de marché" bien connus.
2. **Favicon du site officiel** (PNG, récupéré automatiquement) pour les outils absents de simple-icons mais ayant un site public — la plupart des applications internes ANS, éditeurs RH/finance français, produits Microsoft/Dell/Fortinet, etc.
3. **`logos/placeholder.svg`** (gris générique) en dernier recours, seulement pour 3 outils dont le favicon n'a pas pu être récupéré (Modelio, Greenbone, Qualiac).

## Logos : cascade simple-icons → favicon → placeholder

**1. simple-icons** — les SVG sont livrés en une seule couleur, sans
attribut `fill` (noir par défaut). Un attribut `fill="#RRGGBB"` est ajouté
sur la balise `<svg>` racine avec la couleur de marque officielle :

1. Télécharger le SVG monochrome :
   `https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/<slug>.svg`
2. Trouver sa couleur officielle (hex) dans
   `https://cdn.jsdelivr.net/npm/simple-icons@latest/data/simple-icons.json`
   (chercher par `slug`, champ `hex`).
3. Ajouter `fill="#<hex>"` juste après `<svg` dans le fichier.

Quelques marques (ex. la suite Microsoft 365, AWS, OpenAI, Power BI, Adobe,
Canva, Dynamics 365) ont un fichier d'icône dans simple-icons mais
n'apparaissent plus dans son catalogue de couleurs officielles (retraits
pour raisons de marque) — dans ce cas une couleur de marque publique et
largement documentée a été utilisée manuellement.

**2. Favicon** — pour un outil absent de simple-icons, le favicon de son
site officiel est récupéré via le service de Google (fonctionne aussi pour
les sites sans `/favicon.ico` classique) :

```
https://www.google.com/s2/favicons?domain=<domaine>&sz=128
```

Le fichier est enregistré en PNG sous `logos/favicon-<domaine-avec-tirets>.png`
(ex. `favicon-esante-gouv-fr.png` pour `esante.gouv.fr`). Plusieurs outils
partageant le même domaine (ex. toutes les applications internes ANS sans
site dédié, réglées sur `esante.gouv.fr`) réutilisent le même fichier.

**3. Placeholder** — si ni simple-icons ni le favicon ne donnent de
résultat exploitable (404, icône générique non pertinente), l'outil garde
`logos/placeholder.svg`.

## Ajouter ou modifier un outil

```yaml
- name: Nom de l'outil
  homepage_url: https://outil.exemple.fr
  logo: nom-outil.svg
  description: "Une phrase expliquant à quoi sert l'outil et pour qui."
  project: recommande   # recommande | alternative | deprecie
```

1. Choisissez un statut (`project`) :
   - `recommande` — le choix par défaut de l'ANS pour ce besoin
   - `alternative` — accepté, utilisable dans des contextes spécifiques
   - `deprecie` — à éviter pour de nouveaux usages / en cours de sortie
2. Déposez un logo dans `logos/` (SVG recoloré via simple-icons de
   préférence, sinon un favicon PNG via le service Google — voir section
   précédente), nommé comme la valeur du champ `logo`.
3. Gardez la description à une seule phrase.
4. Ne dépassez pas 5 outils par sous-catégorie — c'est ce qui garde le
   landscape lisible et utile pour trancher rapidement.

Aucun champ `repo_url`/`crunchbase` n'est nécessaire : ces champs ne
concernent que les enrichissements GitHub/Crunchbase (facultatifs) et sont
volontairement omis ici.

## Construire et prévisualiser en local

Installer le CLI `landscape2` (binaire pré-compilé, aucun besoin de Rust /
wasm-pack / yarn) :

```powershell
irm https://github.com/cncf/landscape2/releases/download/v1.1.0/landscape2-installer.ps1 | iex
```

Puis, depuis ce dossier (`landscape/`) :

```powershell
landscape2 build --data-file data.yml --settings-file settings.yml --guide-file guide.yml --logos-path logos --output-dir build
landscape2 serve --landscape-dir build
```

Ouvrir <http://127.0.0.1:8000>.

## Valider les fichiers

```powershell
landscape2 validate data --data-file data.yml
landscape2 validate settings --settings-file settings.yml
landscape2 validate guide --guide-file guide.yml
```

## Déploiement (GitHub Pages)

Le workflow `.github/workflows/deploy-landscape.yml` valide, construit et
déploie automatiquement le contenu de `landscape/` sur GitHub Pages (ou
manuellement via "Run workflow" dans l'onglet Actions).

Ce dépôt contient des personnalisations de l'interface web de landscape2
(`ui/webapp/`, ex. le nom de l'outil et la couleur de statut affichés sur
chaque tuile en vue grille) qui n'existent pas dans l'image officielle CNCF
`ghcr.io/cncf/landscape2`. Le workflow **construit donc lui-même** l'image
`landscape2` à partir de `crates/cli/Dockerfile` (étape "Build landscape2
image from source", avec cache GitHub Actions pour accélérer les
reconstructions suivantes), sur un runner `ubuntu-latest` standard, à chaque
push sur `main`.

> [!IMPORTANT]
> `.github/workflows/build-images.yml` et `.github/workflows/ci.yml`
> utilisent `runs-on: labels: ubuntu-latest-8-cores`, un runner personnalisé
> qui n'est pas disponible sur ce dépôt (leurs jobs restent bloqués en file
> d'attente indéfiniment ou sont annulés — vérifié via l'API GitHub Actions).
> `deploy-landscape.yml` évite volontairement toute dépendance à ces deux
> workflows pour cette raison. Si `ubuntu-latest-8-cores` est configuré côté
> organisation par la suite, `ci.yml`/`build-images.yml` redeviendront
> fonctionnels sans changement nécessaire ici.

Le site est publié comme "project site" à
<https://ansforge.github.io/ans-landscape>, d'où le `base_path: /ans-landscape`
dans `settings.yml`. Si un domaine personnalisé est configuré à la racine,
retirer `base_path` et mettre à jour `url`.

**Étape unique à faire manuellement** (le workflow ne peut pas la faire à
votre place) : dans les réglages du dépôt GitHub, **Settings → Pages →
Build and deployment → Source**, sélectionner **GitHub Actions**.

## À faire avant mise en production

- [ ] Valider les statuts recommande/alternative/deprecie déduits automatiquement de l'inventaire (une présence dans le CMDB ne garantit pas qu'un outil doive rester "recommandé")
- [ ] Revoir/retirer les ~40 outils de marché illustratifs (surtout dans Développement, DevOps, IA, Données, APIs) qui ne viennent pas de l'inventaire réel
- [ ] Fournir un logo officiel pour Modelio, Greenbone et Qualiac (les 3 seuls outils encore en `placeholder.svg`) et vérifier que les favicons récupérés automatiquement restent pertinents
- [ ] Remplacer les couleurs de `settings.yml` par la charte graphique ANS
- [x] Ajouter le logo ANS dans `settings.yml` (`header.logo`) — `logos/ans.png`, affiché en haut à gauche
- [ ] Éventuellement aussi l'afficher en pied de page (`footer.logo`, actuellement commenté dans `settings.yml`)
- [ ] Mettre à jour `url` dans `settings.yml` avec l'URL définitive d'hébergement
- [ ] Activer GitHub Pages avec la source "GitHub Actions" (Settings → Pages)
