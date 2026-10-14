# Limites connues et roadmap

## Limites actuelles (v1)

- **Relance à 24 h** : `Check Status` ne vérifie pas encore si la commande a été confirmée. La relance part pour toutes les commandes.
- **Recherche produit** : correspondance exacte sur le nom (pas de tolérance aux fautes ni aux synonymes).
- **Pas de mémoire de conversation** : chaque message est traité isolément.
- **Stock** : non décrémenté après une commande.
- **Scoring** : calculé mais non enregistré.
- **Webhook non authentifié** : n'importe qui connaissant l'URL peut l'appeler.
- **Format d'entrée** : adaptation nécessaire pour le webhook natif de Green API (voir installation).
- **Un seul produit par message**.

## Pistes d'amélioration

- Authentification du webhook (Header Auth ou token secret dans l'URL)
- Colonne `status` dans `Orders` (pending / confirmed / cancelled) et relance conditionnée dessus
- Décrémentation automatique du stock
- Enregistrement du `lead_score` dans le Sheet
- Recherche floue de produits
- Agent IA avec mémoire de conversation
- Passage de Google Sheets à Supabase ou PostgreSQL
- Tableau de bord (Looker Studio)
