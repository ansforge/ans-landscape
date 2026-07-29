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

## Ajouter un outil réel

Chaque sous-catégorie de `data.yml` contient actuellement un item
`"Exemple à remplacer"` — à remplacer ou dupliquer (max 5 par sous-catégorie) :

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
   la valeur du champ `logo`.
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

- [ ] Remplacer les 20 items `"Exemple à remplacer"` par les outils réels de l'ANS
- [ ] Ajouter les logos SVG correspondants dans `logos/`
- [ ] Remplacer les couleurs de `settings.yml` par la charte graphique ANS
- [ ] Ajouter le logo ANS dans `settings.yml` (`header.logo` / `footer.logo`)
- [ ] Mettre à jour `url` dans `settings.yml` avec l'URL définitive d'hébergement
- [ ] Activer GitHub Pages avec la source "GitHub Actions" (Settings → Pages)
