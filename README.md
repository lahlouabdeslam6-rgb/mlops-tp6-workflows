# TP06 - Automatiser les workflows MLOps

**Zakaria Abdelbaki, Amine Khabot, Ismail Lahlou et Rayane Hafidi - AI, G6**

[![pipeline-mlops](https://github.com/lahlouabdeslam6-rgb/mlops-tp6-workflows/actions/workflows/ci.yml/badge.svg)](https://github.com/lahlouabdeslam6-rgb/mlops-tp6-workflows/actions)

Suite du TP3 : DVC prépare, entraîne et évalue ; Prefect orchestre ces étapes,
applique le seuil F1 et journalise une notification uniquement après acceptation.
GitHub Actions contrôle chaque push dans un environnement Linux indépendant.
Historique et tags du TP3 conservés. Voir [la provenance](ATTRIBUTION.md).

## Installation (PowerShell)

```powershell
git clone https://github.com/lahlouabdeslam6-rgb/mlops-tp6-workflows.git
cd mlops-tp6-workflows
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python donnees.py
python nouvelles_donnees.py
dvc repro
python verifier_qualite.py
```

Recréer la v1 avant chaque ajout v2 pour ne pas ajouter plusieurs fois les mêmes
2 000 lignes. MLflow est omis de cet environnement : aucune étape TP6 ne l'utilise.
Les scripts et l'historique MLflow du TP3 sont conservés.
Le remote DVC local du TP3 peut être absent après clonage ; les scripts recréent
alors les données, sans modifier le dépôt TP3 d'origine.

## Prefect : trois terminaux

Dans chaque terminal : ouvrir ce dossier et activer le venv.

1. `prefect server start --host 127.0.0.1` (serveur et interface sur 4200).
2. `$env:PREFECT_API_URL='http://127.0.0.1:4200/api'`, puis `python flow_mlops.py`.
3. Même variable d'API, puis `python planifier.py` ; ouvrir Deployments et utiliser
   **Run > Quick run** sur `reentrainement-fraude/hebdomadaire`.

`serve` planifie le lundi à 6 h UTC. Garder le processus actif pour exécuter.
Sans ce processus, un run manuel reste en attente. Les démonstrations sont mises
en pause à la fin pour éviter une file d'attente sur une machine arrêtée.

Pour le pool de processus, dans le terminal 2 :

```powershell
$env:TP6_PROJECT_DIR=(Get-Location).Path
$env:PYTHONIOENCODING='utf-8'
prefect work-pool create pool-tp --type process
prefect --no-prompt deploy --name hebdomadaire-pool
```

Dans le terminal 3 : `prefect worker start --pool pool-tp --name worker-tp6`.
Quick run du déploiement pool. Celui-ci suit le lundi à 6 h **Africa/Casablanca**.
Redéployer après déplacement du clone : le chemin est résolu au déploiement.

## Qualité et intégration continue

`max_depth: 12` est l'état normal ; `max_depth: 2` est l'essai volontairement
dégradé. Le seuil est 0,82. Après l'essai, restaurer 12 et rejouer le flow.
Pour la CI, le commit dégradé est annulé par `git revert`, sans réécriture.
La notification du TP est un journal ; aucun message ou e-mail n'est envoyé.

Les rapports et preuves d'exécution sont dans `reports/`.

## Dépôts du groupe (ordre demandé)

1. Zakaria Abdelbaki : https://github.com/ZED-abd/TP06_Automatiser_Workflows_MLOps
2. Amine Khabot : https://github.com/Amine-kh1/mlops-tp6-workflows
3. Ismail Lahlou : https://github.com/lahlouabdeslam6-rgb/mlops-tp6-workflows
4. Rayane Hafidi :
