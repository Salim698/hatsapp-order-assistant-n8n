# Tests

Utilise l'URL de test (`/webhook-test/...`) après avoir cliqué sur **Listen for test event** dans le nœud Webhook.

## Commande

```bash
curl -X POST https://<ton-n8n>/webhook-test/whatsapp-webhook \
  -H "Content-Type: application/json" \
  -d '{"message":"Bonjour, je veux 2 Produit A","phone":"22670000000","name":"Awa"}'
```

Attendu : ligne ajoutée dans `Orders`, confirmation au client, alerte au vendeur.

## Question de stock

```bash
curl -X POST https://<ton-n8n>/webhook-test/whatsapp-webhook \
  -H "Content-Type: application/json" \
  -d '{"message":"Est-ce que Produit A est disponible ?","phone":"22670000000","name":"Awa"}'
```

Attendu : réponse "disponible" avec le stock. Avec `Produit B` (stock 0) : réponse "rupture".

## Support

```bash
curl -X POST https://<ton-n8n>/webhook-test/whatsapp-webhook \
  -H "Content-Type: application/json" \
  -d '{"message":"Mon colis n est jamais arrivé","phone":"22670000000","name":"Awa"}'
```

Attendu : accusé de réception au client et alerte à l'admin.

## Dépannage

| Symptôme | Cause probable |
|---|---|
| Aucun message WhatsApp reçu | Variables Green API incorrectes ou instance non connectée |
| Erreur 400 sur Gemini | Modèle indisponible ou clé invalide |
| Tout part en `support` | Gemini renvoie un JSON invalide : regarder la sortie de `Gemini Classifier` |
| `access to env vars denied` | Voir `N8N_BLOCK_ENV_ACCESS_IN_NODE` dans l'installation |
| Aucune réponse sur une question produit | Nom de produit différent de `ProductName` dans le Sheet |
