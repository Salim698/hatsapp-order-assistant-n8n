# Description des nœuds

## Entrée et analyse

| Nœud | Type | Rôle |
|---|---|---|
| Webhook WhatsApp | Webhook (POST) | Point d'entrée : reçoit `message`, `phone`, `name` |
| Extract Message | Code | Normalise les champs, nom par défaut "Client" |
| Gemini Classifier | HTTP Request | Appelle Gemini, demande un JSON `{intent, product, quantity, confidence}` |
| Parse AI Response | Code | Parse le JSON, réinjecte téléphone et nom, force `support` si intent inconnu ou JSON invalide |
| Router Intent | Switch | Oriente vers `order`, `question` ou `support` |

## Branche `order`

| Nœud | Rôle |
|---|---|
| Save Order | Ajoute la commande dans l'onglet `Orders` |
| Confirm Client | Message de confirmation WhatsApp au client |
| Notify Seller | Alerte WhatsApp au vendeur (`ADMIN_PHONE`) |
| Wait 24h | Pause de 24 heures |
| Check Status | Condition avant relance (voir limites) |
| Auto Follow-up | Message de relance au client |
| Scoring Client | Score de lead : confiance > 0.8 (+3), quantité > 1 (+2), intent order (+5) |

## Branche `question`

| Nœud | Rôle |
|---|---|
| Check Product | Cherche le produit dans l'onglet `Products` (correspondance exacte sur `ProductName`) |
| IF Product Exists | Stock > 0 ? |
| Reply Available | Confirme la disponibilité et la quantité en stock |
| Reply Not Available | Annonce la rupture |

## Branche `support`

| Nœud | Rôle |
|---|---|
| Auto Reply Support | Accusé de réception au client |
| Notify Admin | Transmet le message à l'admin |

## Format de sortie attendu de Gemini

```json
{
  "intent": "order | question | support",
  "product": "nom du produit",
  "quantity": 1,
  "confidence": 0.95
}
```
