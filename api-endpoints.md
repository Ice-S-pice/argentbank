# Transactions API – Phase 2 (design)

## Objectifs (mail d’Avery)
- Voir toutes les transactions du **mois en cours**, **groupées par compte**.
- Voir le **détail** d’une transaction.
- **Modifier** les informations d’une transaction (notes, catégorie, libellé/merchant).
- ⚠️ Pas d’ajout ni de suppression de transaction.

## Sécurité
Toutes les routes Transactions sont protégées par un JWT Bearer via l’en-tête `Authorization: Bearer <token>`.

## Endpoints

### 1) Lister (groupé par compte)
**GET** `/transactions?month=YYYY-MM&groupBy=account&page=1&limit=25`  
- **Description** : retourne les transactions de l’utilisateur pour le mois demandé, **groupées par compte** si `groupBy=account`.  
- **Réponses** : `200` → `GroupedTransactionsResponse`, `401`, `400`, `500`.

### 2) Comptes + transactions par compte
**GET** `/accounts` — liste les comptes de l’utilisateur.  
**GET** `/accounts/{accountId}/transactions?month=YYYY-MM&page&limit` — transactions d’un compte pour le mois demandé.  
- **Réponses** : `200` → `AccountsResponse` / `AccountTransactionsResponse`, `401`, `404`, `500`.

### 3) Détail d’une transaction
**GET** `/transactions/{id}`  
- **Réponses** : `200` → `ApiResponseTransaction`, `401`, `404`, `500`.

### 4) Mise à jour des métadonnées
**PATCH** `/transactions/{id}`  
- **Body** : `{ "category"?: string, "notes"?: string, "label"?: string }`  
- **Réponses** : `200` → `ApiResponseTransaction`, `400`, `401`, `404`, `500`.

## Justification
- **groupBy=account** répond directement à l’écran “mois courant groupé par compte”.
- **PATCH** (et non PUT) car on met à jour **partiellement** des champs non sensibles (pas de montant, pas de date).
- **Pas de POST/DELETE** : conforme à la consigne “on n’ajoute/supprime pas de transaction”.
- **Mois courant par défaut** pour coller à l’UX sans obliger le paramètre `month`.
