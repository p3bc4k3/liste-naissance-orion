# Liste de naissance d'Orion

Site web de liste de naissance auto-hébergé : cagnotte Leetchi mise en avant + liste d'objets réservables par les proches, visible en temps réel.

## Architecture

- **Frontend** : `liste-naissance.html`, 100% statique (HTML/CSS/JS vanilla), thème constellation d'Orion / mythologie grecque.
- **Backend** : N8N (instance déjà en place sur le homelab), workflow `n8n-workflow-liste-naissance.json` avec deux routes sur un seul webhook :
  - `GET /liste-naissance` → lit `objets.json`, renvoie la liste
  - `POST /liste-naissance` avec `{ id, action: "reserve" }` → marque l'objet réservé, réécrit le fichier
- **Stockage** : fichier JSON sur le serveur N8N (`/data/liste-naissance/objets.json`), pas de base de données. Un exemple de structure est fourni dans `data/objets.json`.

## Déploiement

1. Copier `data/objets.json` (avec la vraie liste de cadeaux) vers `/data/liste-naissance/objets.json` sur le serveur.
2. Importer `n8n-workflow-liste-naissance.json` dans N8N, vérifier le chemin du fichier, activer le workflow.
3. Vérifier `WEBHOOK_URL` dans `liste-naissance.html` (actuellement `https://n8n.delonca.com/webhook/liste-naissance`).
4. Héberger `liste-naissance.html` (Nginx statique ou GitHub Pages).
5. Configurer le sous-domaine OVH (ex. `naissance.delonca.com`) en reverse proxy vers le webhook N8N.

## Reste à faire

- [ ] Remplir `data/objets.json` avec la vraie liste de cadeaux
- [ ] Importer et activer le workflow N8N
- [ ] Héberger le HTML + configurer le sous-domaine OVH
- [ ] (Optionnel) Ajouter une protection basique sur le webhook contre les réservations frauduleuses

⚠️ "Orion" est un prénom confirmé pour ce projet (utilisé tel quel dans le HTML).
