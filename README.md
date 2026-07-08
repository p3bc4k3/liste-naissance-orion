# Liste de naissance d'Orion

Site web de liste de naissance auto-hébergé : cagnotte Leetchi mise en avant + liste d'objets réservables par les proches, visible en temps réel.

## Architecture

- **Frontend** : `index.html`, 100% statique (HTML/CSS/JS vanilla), thème constellation d'Orion / mythologie grecque. Hébergé via GitHub Pages.
- **Backend** : N8N (instance déjà en place sur le homelab), workflow `n8n-workflow-liste-naissance.json` avec deux routes sur un seul webhook :
  - `GET /liste-naissance` → lit le Google Sheet, renvoie la liste au format `objets[]`
  - `POST /liste-naissance` avec `{ id, action: "reserve", nom, prenom, message }` → met `reserve = TRUE` sur la ligne correspondante dans le Sheet, relit et renvoie la liste à jour, et envoie en parallèle un email à `jean.delonca@gmail.com` (nœud "Envoyer email (don)") avec le prénom du donateur et son message
- **Stockage** : [Google Sheet "Liste naissance"](https://docs.google.com/spreadsheets/d/1DBBzZXJXa3zy3XtvXqIyaMRYF3Y_S36UZrLk8L_Zr7E/edit?gid=0#gid=0), colonnes `id | nom | lien | prix | reserve`. Choisi à la place d'un simple fichier JSON pour permettre l'édition directe de la liste (ajout/retrait de cadeaux, prix, liens) sans toucher au serveur.
  - Un exemple de structure (ancienne approche fichier) reste dans `data/objets.json` à titre de référence, non utilisé par le workflow actuel.

## Déploiement

1. Dans N8N, connecter des identifiants Google Sheets OAuth2 (Credentials → Google Sheets), puis les sélectionner sur les 3 nodes Google Sheets du workflow (le champ `credentials` du JSON exporté est un placeholder, il faut le relier manuellement après import).
2. Connecter également des identifiants Gmail OAuth2 sur le node "Envoyer email (don)" (même principe, placeholder à relier après import).
3. Importer `n8n-workflow-liste-naissance.json` dans N8N, vérifier `documentId` / `sheetName` sur chaque node Google Sheets, activer le workflow.
4. Vérifier `WEBHOOK_URL` dans `index.html` (actuellement `https://n8n.delonca.com/webhook/liste-naissance`).
5. Hébergé via GitHub Pages (activé sur ce repo, branche `main`, racine `/`).
6. Configurer le sous-domaine OVH (ex. `naissance.delonca.com`) en reverse proxy vers le webhook N8N.

## Gestion de la liste

La liste se modifie directement dans le Google Sheet : ajouter une ligne = ajouter un cadeau (incrémenter `id`), remplir `nom`/`lien`/`prix`, laisser `reserve` vide ou `FALSE`. Ne pas partager le Sheet en édition avec les invités — seul le webhook N8N doit pouvoir y écrire, pour garder le contrôle sur les réservations.

Note sur le brouillon initial : le kit "BEBECONFORT Trousse de toilette (8 produits)" a été éclaté en 8 lignes séparées et réservables individuellement (Tétines, Bavoirs, Coussinets d'allaitement, Thermomètre de bain, Brosse à cheveux, Kit ongles bébé, Thermomètre médical, Tapis d'éveil), plutôt que gardé comme un seul lot groupé — à ajuster dans le Sheet si ce n'est pas ce qui était voulu.

## Reste à faire

- [ ] Compléter `lien` et `prix` pour chaque ligne du Google Sheet
- [ ] Connecter les identifiants Google Sheets OAuth2 dans N8N et importer/activer le workflow
- [ ] Connecter les identifiants Gmail OAuth2 sur le node "Envoyer email (don)"
- [x] Héberger le HTML + configurer le sous-domaine OVH
- [ ] (Optionnel) Ajouter une protection basique sur le webhook contre les réservations frauduleuses

⚠️ "Orion" est un prénom confirmé pour ce projet (utilisé tel quel dans le HTML).
