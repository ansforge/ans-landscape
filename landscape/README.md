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

`data.yml` contient actuellement 54 outils réels et connus du marché
(Jira, GitLab, Kubernetes, Grafana, Keycloak, Mistral AI, etc.), répartis
dans les 25 sous-catégories, pour montrer à quoi ressemble un landscape
rempli et comment fonctionnent les statuts. **Ces choix sont illustratifs**
(populaires/représentatifs du marché) — ils ne reflètent pas une décision
officielle de l'ANS. Il faut les revoir et les ajuster (statuts, outils
présents/absents) selon la politique réelle de l'agence avant mise en
production.

La plupart des logos proviennent du projet
[simple-icons](https://simpleicons.org) (licence CC0, réutilisation libre) ;
deux outils (GLPI, ServiceNow) n'y ont pas de logo disponible et utilisent
`logos/placeholder.svg` en attendant un logo officiel.

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

- [ ] Revoir les 54 outils d'exemple (présence, statut recommande/alternative/deprecie) et les remplacer par les choix réels/officiels de l'ANS
- [ ] Fournir un logo pour GLPI et ServiceNow (actuellement `placeholder.svg`)
- [ ] Remplacer les couleurs de `settings.yml` par la charte graphique ANS
- [ ] Ajouter le logo ANS dans `settings.yml` (`header.logo` / `footer.logo`)
- [ ] Mettre à jour `url` dans `settings.yml` avec l'URL définitive d'hébergement
- [ ] Activer GitHub Pages avec la source "GitHub Actions" (Settings → Pages)
