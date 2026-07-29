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
- `logos/` — un fichier SVG par outil, référencé par son nom de fichier dans `data.yml`

## Contenu actuel : des exemples, pas les choix officiels de l'ANS

`data.yml` contient actuellement 66 outils réels et connus du marché,
répartis dans 8 catégories (dont "APIs & Interopérabilité"), pour montrer à
quoi ressemble un landscape rempli et comment fonctionnent les statuts.
**Ces choix sont illustratifs** — ils ne reflètent pas une décision
officielle de l'ANS. Il faut les revoir et les ajuster (statuts, outils
présents/absents) selon la politique réelle de l'agence avant mise en
production.

Les logos proviennent du projet [simple-icons](https://simpleicons.org)
(licence CC0, réutilisation libre), recolorés avec la couleur de marque
officielle de chaque éditeur (voir section suivante). 7 outils n'ont pas de
logo disponible dans simple-icons et utilisent `logos/placeholder.svg` en
attendant un logo officiel : Microsoft Planner, Squash TM, Loki, Microsoft
365 Copilot, Gravitee, ReadyAPI, Microsoft Entra ID, ainsi que GLPI.

## Logos en couleur

Les fichiers SVG de simple-icons sont livrés en une seule couleur, sans
attribut `fill` (ils apparaissent noirs par défaut). Pour obtenir des logos
en couleur, un attribut `fill="#RRGGBB"` a été ajouté sur la balise `<svg>`
racine de chaque fichier, avec la couleur de marque officielle (hex).
Pour un nouvel outil trouvé sur simple-icons :

1. Télécharger le SVG monochrome :
   `https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/<slug>.svg`
2. Trouver sa couleur officielle (hex) dans
   `https://cdn.jsdelivr.net/npm/simple-icons@latest/data/simple-icons.json`
   (chercher par `slug`, champ `hex`).
3. Ajouter `fill="#<hex>"` juste après `<svg` dans le fichier.

Quelques marques (ex. la suite Microsoft 365, AWS, OpenAI, Power BI) ont un
fichier d'icône dans simple-icons mais n'apparaissent plus dans son
catalogue de couleurs officielles (retraits pour raisons de marque) — dans
ce cas une couleur de marque publique et largement documentée a été utilisée
manuellement.

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
2. Déposez un logo au format **SVG uniquement** dans `logos/`, nommé comme
   la valeur du champ `logo` (essayer `https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/<slug>.svg`
   avant de fabriquer un logo, beaucoup d'éditeurs y sont déjà référencés).
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
déploie automatiquement le contenu de `landscape/` sur GitHub Pages à chaque
push sur `main` qui touche ce dossier (ou manuellement via
"Run workflow" dans l'onglet Actions). Il utilise l'image officielle
`ghcr.io/cncf/landscape2` — aucune installation locale n'est nécessaire côté CI.

Le site est publié comme "project site" à
<https://ansforge.github.io/ans-landscape>, d'où le `base_path: /ans-landscape`
dans `settings.yml`. Si un domaine personnalisé est configuré à la racine,
retirer `base_path` et mettre à jour `url`.

**Étape unique à faire manuellement** (le workflow ne peut pas la faire à
votre place) : dans les réglages du dépôt GitHub, **Settings → Pages →
Build and deployment → Source**, sélectionner **GitHub Actions**.

## À faire avant mise en production

- [ ] Revoir les 66 outils d'exemple (présence, statut recommande/alternative/deprecie) et les remplacer par les choix réels/officiels de l'ANS
- [ ] Fournir un logo pour les 7 outils en `placeholder.svg` (Microsoft Planner, Squash TM, Loki, Microsoft 365 Copilot, Gravitee, ReadyAPI, Microsoft Entra ID) et pour GLPI
- [ ] Remplacer les couleurs de `settings.yml` par la charte graphique ANS
- [ ] Ajouter le logo ANS dans `settings.yml` (`header.logo` / `footer.logo`)
- [ ] Mettre à jour `url` dans `settings.yml` avec l'URL définitive d'hébergement
- [ ] Activer GitHub Pages avec la source "GitHub Actions" (Settings → Pages)
