# Prédire le prix des EHPAD

Projet de data science (formation Le Wagon) : prédire le **prix mensuel de l'hébergement permanent en chambre seule** (GIR 5-6, 2019) d'un EHPAD à partir de son profil et de son territoire.

## Résultat

- **Erreur moyenne (MAE) : 157 €**, soit environ 7 % du prix moyen (R² = 0,77)
- Erreur divisée par 2,1 par rapport à une prédiction naïve (médiane)
- Évaluation sur 1 227 EHPAD de gestionnaires jamais vus à l'entraînement

## Données

6 sources publiques reliées en une base de **6 673 EHPAD** (France métropolitaine) :

| Source | Apport |
|---|---|
| CNSA – prix EHPAD 2019 | Prix (cible), statut, habilitation ASH, places |
| FINESS (Etalab) | Commune, gestionnaire, date d'ouverture |
| INSEE – recensement 2019 | Population, part des 75 ans et plus |
| Composition des EPCI 2020 | Rattachement commune → intercommunalité |
| Filosofi 2019 | Revenu et patrimoine des 75 ans et plus |
| Carte des loyers | Loyer prédit au m² par commune |

## Démarche

1. **`01_consolidation.ipynb`** : jointure et nettoyage des sources (retrait des DOM, des îles sans EPCI et des prix manquants ; dates d'ouverture aberrantes remplacées par la médiane).
2. **`02_exploration.ipynb`** : analyse du prix (forte asymétrie, poids du statut et du loyer, « effet réseau » des gestionnaires) et sélection de 12 variables.
3. **`03_modelisation.ipynb`** : comparaison de 7 approches (baseline, régressions linéaires, Random Forest, Gradient Boosting…). Découpage train/test **par gestionnaire** pour éviter les fuites.

## Principaux enseignements

- Le **statut juridique** explique à lui seul 53 % des écarts (≈ 850 € d'écart médian public / privé commercial).
- Le **loyer au m²** de la commune est la variable de territoire la plus liée au prix.
- Le modèle se trompe surtout sur les **EHPAD privés commerciaux** (erreur moyenne de 259 €), faute de données sur le standing et les services.

## Structure

```
Datacase/
├── data/
│   ├── raw/        # sources brutes
│   └── clean/      # ehpad.csv, modele.csv
├── notebooks/      # 01 → 03
├── Prix_EHPAD_presentation_TL.pptx
└── requirements.txt
```

## Installation

```bash
python -m venv .venv
.venv\Scripts\activate      # Windows
pip install -r requirements.txt
```

## Limites et pistes

- Prix de 2019 ; DOM et îles exclus ; standing et état du bâtiment absents.
- Pistes : département en variable catégorielle dans le Gradient Boosting, indicateurs de ruralité, un modèle par statut.
