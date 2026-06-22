<div align="center">
  <img src="assets/logo.png" alt="Aube Digital" width="96">

  # Aube Digital — Temps

  **Gestionnaire de temps personnel pour freelances.**
  *L'IA est un outil, pas une menace.*
</div>

---

## À propos

**Aube Digital — Temps** est un outil léger de gestion de temps conçu pour un développeur web freelance jonglant entre prospection, missions clients, formation et création de contenu. Pas de compte à gérer, pas de framework lourd : un seul fichier HTML, du JavaScript vanilla, et Supabase pour la synchronisation entre appareils.

Construit et maintenu par [Aube Digital](https://github.com/bast5577438).

## Fonctionnalités

- **Tâches & planning hebdomadaire** — on pose la semaine à l'avance, on confirme chaque jour la veille.
- **Catégories personnalisables** — Prospection, Client, Formation, Aube Digital, Perso (modifiables dans le code).
- **Durée estimée par tâche** — pas de chrono temps réel, juste de l'estimé à cocher une fois fait.
- **Dashboard du jour** — tâches en retard, rappel de confirmation du lendemain, objectifs hebdo par catégorie (heures faites vs objectif).
- **Synchronisation multi-appareils** via Supabase (Postgres + Realtime), sans création de compte classique.
- **Responsive** mobile et desktop, identité visuelle Aube Digital (dégradé aurore, Space Grotesk + Inter).

## Stack technique

- HTML / CSS / JavaScript vanilla — aucun framework, aucune étape de build.
- [Supabase](https://supabase.com/) (Postgres + Realtime + Authentication anonyme) pour la persistance et la synchro.
- Déploiement statique sur [Vercel](https://vercel.com/) (le projet Supabase a été créé via l'intégration Vercel → Storage → Supabase).

## Installation

### 1. Configurer Supabase

1. Crée un projet Supabase (directement via Vercel → ton projet → **Storage** → **Browse Marketplace** → **Supabase** → **Create New**, ou sur [supabase.com](https://supabase.com)).
2. Dans **Settings → API**, récupère l'**URL** du projet et la clé **anon / public** (jamais `service_role`, qui est secrète).
3. Colle ces deux valeurs dans `index.html`, en haut du `<script type="module">`, dans `const supabaseConfig = { ... }`.
4. Dans **SQL Editor**, exécute :

   ```sql
   create extension if not exists pgcrypto;

   create table public.blocks (
     id uuid primary key default gen_random_uuid(),
     space_id text not null,
     title text not null,
     category text not null,
     date date not null,
     time time,
     duration integer,
     done boolean not null default false,
     confirmed boolean not null default false,
     created_at timestamptz not null default now()
   );

   create table public.goals (
     space_id text primary key,
     prospection numeric not null default 8,
     client numeric not null default 15,
     formation numeric not null default 5,
     aube numeric not null default 4,
     perso numeric not null default 3,
     updated_at timestamptz not null default now()
   );

   alter table public.blocks enable row level security;
   alter table public.goals enable row level security;

   create policy "authenticated full access" on public.blocks
     for all using (auth.uid() is not null) with check (auth.uid() is not null);
   create policy "authenticated full access" on public.goals
     for all using (auth.uid() is not null) with check (auth.uid() is not null);

   alter publication supabase_realtime add table public.blocks;
   alter publication supabase_realtime add table public.goals;
   ```

5. Dans **Authentication → Sign In / Providers**, active **Allow anonymous sign-ins** → **Save changes**.

### 2. Lancer en local

```bash
npx serve .
```

ou ouvre simplement `index.html` dans un navigateur.

### 3. Déployer sur Vercel

Importe ce repo depuis le [dashboard Vercel](https://vercel.com/new) — c'est un site 100% statique, aucune configuration supplémentaire n'est nécessaire.

## Fonctionnement

L'app n'a pas de système de compte classique. Au premier lancement, on choisit un **code d'accès personnel** : il sert de clé de partitionnement des données dans Postgres (colonne `space_id` sur chaque table) et doit être saisi à l'identique sur chaque appareil pour retrouver ses données.

## ⚠️ Sécurité

Ce projet étant public, le code source — y compris les policies RLS — est lisible par tout le monde. La protection des données ne repose **pas** sur un compte/mot de passe classique, mais sur :

- l'authentification anonyme Supabase (bloque les appels directs sans session), et
- le **code d'accès personnel**, qui doit rester confidentiel et suffisamment difficile à deviner (éviter un simple prénom + année).

Les clés `service_role`, `secret`, le JWT secret et le mot de passe Postgres ne doivent **jamais** apparaître dans ce dépôt — uniquement dans les variables d'environnement Vercel.

Ce n'est pas un niveau de sécurité "entreprise" — adapté à un usage personnel, pas à des données sensibles ou réglementées.

## Limites connues (MVP)

- Toute la collection `blocks` est chargée en une fois (pas de filtrage par date côté serveur) — largement suffisant pour un usage perso.
- Pas de compte multi-utilisateurs : un seul code d'accès par "espace" de données.

---

<div align="center">
  <sub>© Aube Digital — Bastien Vannière</sub>
</div>
