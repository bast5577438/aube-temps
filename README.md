<div align="center">
  <img src="assets/logo.png" alt="Aube Digital" width="96">

  # Aube Digital — Temps

  **Gestionnaire de temps personnel pour freelances.**
  *L'IA est un outil, pas une menace.*
</div>

---

## À propos

**Aube Digital — Temps** est un outil léger de gestion de temps conçu pour un développeur web freelance jonglant entre prospection, missions clients, formation et création de contenu. Pas de compte à gérer, pas de framework lourd : un seul fichier HTML, du JavaScript vanilla, et Firebase pour la synchronisation entre appareils.

Construit et maintenu par [Aube Digital](https://github.com/bast5577438).

## Fonctionnalités

- **Tâches & planning hebdomadaire** — on pose la semaine à l'avance, on confirme chaque jour la veille.
- **Catégories personnalisables** — Prospection, Client, Formation, Aube Digital, Perso (modifiables dans le code).
- **Durée estimée par tâche** — pas de chrono temps réel, juste de l'estimé à cocher une fois fait.
- **Dashboard du jour** — tâches en retard, rappel de confirmation du lendemain, objectifs hebdo par catégorie (heures faites vs objectif).
- **Synchronisation multi-appareils** via Firebase Firestore, sans création de compte classique.
- **Responsive** mobile et desktop, identité visuelle Aube Digital (dégradé aurore, Space Grotesk + Inter).

## Stack technique

- HTML / CSS / JavaScript vanilla — aucun framework, aucune étape de build.
- [Firebase](https://firebase.google.com/) (Firestore + Authentication anonyme) pour la persistance et la synchro.
- Déploiement statique sur [Vercel](https://vercel.com/).

## Installation

### 1. Configurer Firebase

1. Crée un projet sur la [console Firebase](https://console.firebase.google.com).
2. Ajoute une application **Web** (`</>`) et récupère l'objet `firebaseConfig`.
3. Colle-le dans `index.html`, en haut du `<script type="module">`, à la place du `firebaseConfig` existant.
4. Active **Firestore Database** (mode production) et publie ces règles :

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

5. Active l'authentification **Anonyme** dans **Authentication → Sign-in method**.

### 2. Lancer en local

```bash
npx serve .
```

ou ouvre simplement `index.html` dans un navigateur.

### 3. Déployer sur Vercel

Importe ce repo depuis le [dashboard Vercel](https://vercel.com/new) — c'est un site 100% statique, aucune configuration supplémentaire n'est nécessaire.

## Fonctionnement

L'app n'a pas de système de compte classique. Au premier lancement, on choisit un **code d'accès personnel** : il sert de clé de partitionnement des données dans Firestore (`spaces/{code}/...`) et doit être saisi à l'identique sur chaque appareil pour retrouver ses données.

## ⚠️ Sécurité

Ce projet étant public, le code source — y compris les règles Firestore — est lisible par tout le monde. La protection des données ne repose **pas** sur un compte/mot de passe classique, mais sur :

- l'authentification anonyme Firebase (bloque les appels directs sans session), et
- le **code d'accès personnel**, qui doit rester confidentiel et suffisamment difficile à deviner (éviter un simple prénom + année).

Ce n'est pas un niveau de sécurité "entreprise" — adapté à un usage personnel, pas à des données sensibles ou réglementées.

## Limites connues (MVP)

- Toute la collection `blocks` est chargée en une fois (pas de filtrage par date côté serveur) — largement suffisant pour un usage perso.
- Pas de compte multi-utilisateurs : un seul code d'accès par "espace" de données.

---

<div align="center">
  <sub>© Aube Digital — Bastien Vannière</sub>
</div>
