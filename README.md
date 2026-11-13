# MAS RISK CYBER

Plateforme d'analyse de risques cyber pilotée par des agents IA. Automatise trois méthodologies reconnues — **EBIOS RM**, **ISO 27005** et **FAIR** — pour produire des rapports structurés avec plans de traitement.

---

## Démarrage rapide

```bash
# 1. Installer les dépendances
pip install -r requirements.txt

# 2. Configurer la clé API
echo ANTHROPIC_API_KEY=sk-ant-... > .env

# 3. Lancer
python app/flask_app.py
```

Ouvrir [http://localhost:5000](http://localhost:5000).

---

## Prérequis

- Python 3.10+
- Une clé API Anthropic ([console.anthropic.com](https://console.anthropic.com))

---

## Méthodologies disponibles

| Méthode | Type d'analyse | Résultat principal |
|---|---|---|
| **EBIOS RM** | Qualitative (5 ateliers ANSSI) | Scénarios stratégiques & opérationnels |
| **ISO 27005** | Semi-quantitative (matrice P×I) | Cartographie des risques par actif |
| **FAIR** | Quantitative (modèle financier) | ALE en euros + ROSI par mesure |

---

## Utilisation

### 1. Sélectionner une méthode

La page d'accueil présente les trois méthodes. Cliquer sur une carte pour accéder au formulaire.

### 2. Saisir le contexte

Chaque méthode dispose d'un formulaire guidé :

**EBIOS RM** — objet de l'étude, valeurs métier, actifs supports, socle de sécurité  
**ISO 27005** — périmètre, actifs (support / information / service), critères CIA  
**FAIR** — description de l'actif, valeur en euros, agent de menace, scénarios de perte

### 3. Lancer l'analyse

Cliquer sur **Analyser**. Un terminal affiche la progression des agents en temps réel. L'analyse dure **2 à 5 minutes** selon la complexité.

### 4. Exporter le rapport

Une fois l'analyse terminée, cliquer sur **Exporter en Word** pour télécharger le rapport `.docx`.

---

## Historique

Les analyses sont sauvegardées automatiquement dans une base SQLite locale (`mas_risk.db`).

| URL | Contenu |
|---|---|
| `/historique` | Analyses EBIOS RM |
| `/historique_iso27005` | Analyses ISO 27005 |
| `/historique_fair` | Analyses FAIR |

---

## Architecture

```
MAS_RISK_CYBER/
├── agents/          # Agents spécialisés + superviseurs (Claude Opus)
├── core/            # Modèles de données, prompts, base de données
├── app/             # Application Flask, templates, CLI
├── api/             # Point d'entrée Vercel
└── formulaires/     # Formulaires de collecte EBIOS RM
```

Les agents sont orchestrés par un superviseur qui valide chaque étape et itère si nécessaire (jusqu'à 2 corrections par phase).

---

## Déploiement (Vercel)

```bash
vercel deploy
```

La configuration dans `vercel.json` route toutes les requêtes vers `api/index.py` avec un timeout de 300 secondes.

---

## Dépannage

| Erreur | Cause | Solution |
|---|---|---|
| `500 Internal Server Error` au démarrage | Clé API manquante | Vérifier le fichier `.env` |
| Analyse bloquée / timeout | Quota API dépassé | Vérifier les limites sur console.anthropic.com |
| Export Word vide | Aucune analyse en base | Lancer une analyse d'abord |
| `ModuleNotFoundError` | Dépendances absentes | `pip install -r requirements.txt` |

---

## Stack technique

- **Backend** — Flask 3.1.2
- **IA** — Claude Sonnet 4 (agents) + Claude Opus (supervision et validation)
- **Base de données** — SQLite3
- **Export** — python-docx
- **Déploiement** — Vercel (serverless)
