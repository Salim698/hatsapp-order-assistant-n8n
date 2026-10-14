# Installation et configuration

## Prérequis

- Une instance n8n (self-hosted recommandé)
- Un compte [Green API](https://green-api.com) avec une instance WhatsApp connectée
- Une clé API Google Gemini (Google AI Studio)
- Un compte Google (Google Sheets)

## 1. Google Sheets

Crée un classeur avec **deux onglets** (les noms comptent).

**Onglet `Products`**

| ProductName | StockQuantity | Price |
|---|---|---|
| Produit A | 25 | 5000 |

**Onglet `Orders`** (ligne 1 = en-têtes)

| phone | name | product | quantity | timestamp |
|---|---|---|---|---|

Des modèles CSV sont dans le dossier `data/`.

Récupère l'ID du classeur dans son URL : `https://docs.google.com/spreadsheets/d/<ID>/edit`.

## 2. Variables d'environnement

Copie `.env.example` et renseigne les valeurs :

| Variable | Description |
|---|---|
| `GEMINI_API_KEY` | Clé API Gemini |
| `GREEN_API_INSTANCE` | ID d'instance Green API |
| `GREEN_API_TOKEN` | Token de l'instance |
| `ADMIN_PHONE` | Numéro du vendeur, format international sans `+` |

Sur n8n self-hosted, injecte-les dans l'environnement du conteneur (Docker : `environment:` ou `--env-file`).

> Les versions récentes de n8n peuvent bloquer l'accès à `$env` dans les nœuds. Si tu vois une erreur "access to env vars denied", ajoute `N8N_BLOCK_ENV_ACCESS_IN_NODE=false`. Sur n8n Cloud, `$env` n'est pas disponible : utilise des credentials n8n à la place.

## 3. Import du workflow

1. n8n > **Workflows > Import from file** > `workflow.json`
2. Dans `Save Order` et `Check Product` : remplace `YOUR_GOOGLE_SHEET_ID_HERE` par l'ID de ton classeur
3. Sélectionne ton credential Google Sheets sur ces deux nœuds

## 4. Green API : recevoir les messages

Le workflow attend ce JSON sur le webhook :

```json
{
  "message": "Bonjour, je veux 2 savons",
  "phone": "22670000000",
  "name": "Awa"
}
```

Le webhook natif de Green API envoie un format différent (`senderData`, `messageData`...). Deux options :

- Placer un nœud intermédiaire qui convertit le format Green API vers celui ci-dessus
- Ou modifier le nœud `Extract Message` pour lire directement les champs Green API

## 5. Activer le workflow

- URL de test : `https://<ton-n8n>/webhook-test/whatsapp-webhook`
- URL de production (workflow activé) : `https://<ton-n8n>/webhook/whatsapp-webhook`

Utilise l'URL de production dans la configuration des notifications de Green API.
