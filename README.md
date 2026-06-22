# Aube Digital — Temps

Gestionnaire de temps perso (tâches + planning hebdo) pour Aube Digital. Un seul fichier `index.html`, vanilla JS, Firebase (Firestore) pour la synchro entre tes appareils, déployable directement sur Vercel.

## 1. Créer le projet Firebase (5 min)

1. Va sur [console.firebase.google.com](https://console.firebase.google.com) → **Ajouter un projet** → nomme-le par ex. `aube-digital-temps` → crée-le (tu peux désactiver Google Analytics, pas utile ici).
2. Dans le projet, clique l'icône **</>** (Web) pour ajouter une appli web → nomme-la `aube-temps` → **Enregistrer l'application**.
3. Firebase t'affiche un objet `firebaseConfig`. Copie-le.
4. Ouvre `index.html`, repère vers le haut du `<script type="module">` :
   ```js
   const firebaseConfig = {
     apiKey: "YOUR_API_KEY",
     ...
   };
   ```
   et remplace-le par celui que Firebase t'a donné.

## 2. Activer Firestore

1. Dans la console Firebase, menu de gauche → **Firestore Database** → **Créer une base de données** → mode **production** → choisis une région proche (ex. `eur3`).
2. Onglet **Règles**, remplace par :
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /spaces/{spaceId}/{document=**} {
         allow read, write: if request.auth != null;
       }
     }
   }
   ```
   → **Publier**.

## 3. Activer l'authentification anonyme

1. Menu de gauche → **Authentication** → **Get started**.
2. Onglet **Sign-in method** → active le fournisseur **Anonyme**.

C'est tout pour Firebase. L'app se connecte silencieusement en anonyme (pas d'écran de login) — la séparation de tes données se fait via le **code d'accès perso** que tu choisis au premier lancement (à retaper sur ton autre appareil pour retrouver les mêmes données). Ce n'est pas un vrai compte sécurisé : ne mets pas ce lien public, et garde ton code pour toi.

## 4. Tester en local

Ouvre simplement `index.html` dans ton navigateur (double-clic), ou avec un petit serveur local :
```
npx serve .
```

## 5. Déployer sur Vercel

```
npx vercel
```
(ou connecte le dossier à un repo Git et importe-le sur vercel.com — c'est un site 100% statique, aucune config Vercel nécessaire).

## Comment ça marche

- **Catégories** : Prospection, Client, Formation, Aube Digital, Perso — codées en dur dans `CATEGORIES` en haut du script si tu veux les changer.
- **Tâches/créneaux** : titre, catégorie, date, heure (optionnelle), durée estimée. Pas de chrono temps réel — juste de l'estimé que tu coches une fois fait.
- **Semaine** : tu poses tes créneaux à l'avance sur la vue "Semaine". Chaque jour a un badge *Vide / À confirmer / Confirmé*. Le bouton "Confirmer" sur un jour marque tous ses créneaux comme validés.
- **Dashboard "Aujourd'hui"** : bannière des tâches en retard (non faites, date passée), bannière "demain à confirmer" si le planning du lendemain n'est pas encore validé, barres d'objectifs hebdo (heures faites vs objectif, réglables via l'icône ⚙), et le planning du jour à cocher.
- **Objectifs hebdo** : réglables dans ⚙ Réglages, en heures par catégorie.
- **Raccourci clavier** : `n` ouvre l'ajout rapide, `Échap` ferme les modales.

## Limites connues (MVP)

- Toute la collection `blocks` est chargée d'un coup (pas de pagination/filtrage par date côté serveur) — largement suffisant pour un usage perso, à revoir si l'historique devient très volumineux.
- Pas de vrai compte/mot de passe : la protection repose sur le code d'accès + l'auth anonyme Firebase.
