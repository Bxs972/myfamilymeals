# 🥗 NutriFamille — Planning de repas familial

Application web de planification des repas de la semaine pour toute la famille, avec synchronisation en temps réel entre les membres du foyer.

**[→ Voir l'application](https://myfamilymeals.vercel.app)**

---

## Fonctionnalités

- **Calendrier hebdomadaire** — vue 7 jours avec petit-déjeuner, déjeuner et dîner
- **Gestion des recettes** — nom du plat, ingrédients avec quantités, étapes de préparation
- **Copie de repas** — dupliquer un repas vers n'importe quel autre créneau en un clic
- **Recherche** — retrouver un plat ou un ingrédient dans tout l'historique
- **Liste de courses** — générée automatiquement depuis les repas planifiés, filtrable par semaine et type de repas
- **Export PDF** — liste de courses mise en page et prête à imprimer
- **Import CSV** — planifier plusieurs repas en masse via un fichier CSV
- **Statut cuisiné** — marquer les repas comme préparés
- **Notes personnelles** — ajouter des remarques sur chaque repas
- **Temps réel** — synchronisation instantanée entre tous les membres via Supabase
- **Mobile** — interface responsive avec navigation en bas d'écran

---

## Stack technique

| Couche | Technologie |
|--------|------------|
| Frontend | HTML / CSS / JavaScript vanilla |
| Base de données | [Supabase](https://supabase.com) (PostgreSQL) |
| Auth | Supabase Auth (email + mot de passe) |
| Temps réel | Supabase Realtime (Postgres Changes) |
| Build / Deploy | Vercel (fichier statique unique) |
| PDF | jsPDF (chargé à la demande) |

---

## Installation locale

### Prérequis
- Un compte [Supabase](https://supabase.com) avec un projet créé

### 1. Cloner le repository

```bash
git clone https://github.com/Bxs972/myfamilymeals.git
cd myfamilymeals
```

### 2. Créer la table Supabase

Dans l'éditeur SQL de votre projet Supabase, exécutez :

```sql
create table meals (
  id          uuid primary key default gen_random_uuid(),
  date        date not null,
  meal_type   text not null check (meal_type in ('breakfast','lunch','dinner')),
  name        text not null,
  ingredients jsonb default '[]',
  steps       jsonb default '[]',
  cooked      boolean default false,
  notes       text default '',
  created_by  uuid references auth.users(id),
  created_at  timestamptz default now(),
  unique (date, meal_type)
);

-- Activer Row Level Security
alter table meals enable row level security;

create policy "Membres authentifiés" on meals
  for all using (auth.role() = 'authenticated');
```

### 3. Configurer les clés API

Dans `nutrifamille.html`, remplacez les deux valeurs en haut du script :

```js
const SB_URL  = 'https://VOTRE-ID.supabase.co';
const SB_ANON = 'VOTRE-CLE-ANON-PUBLIQUE';
```

Ces valeurs se trouvent dans votre tableau de bord Supabase → **Settings → API**.

### 4. Créer un compte utilisateur

Dans Supabase → **Authentication → Users → Invite user**, créez les comptes email pour chaque membre du foyer.

### 5. Ouvrir l'application

Ouvrez simplement `nutrifamille.html` dans un navigateur, ou déployez sur Vercel en connectant ce repository.

---

## Format CSV pour l'import

```
date,repas,nom,ingredients,etapes
2025-05-19,lunch,Poulet rôti,"200g:poulet;1:citron;sel:qsp","Préchauffer 200°C;Cuire 1h15"
2025-05-20,dinner,Doro Wat,"500g:poulet;3:oignons;2cs:berbéré","Revenir oignons 30min;Mijoter 40min"
```

| Colonne | Valeurs acceptées |
|---------|------------------|
| `repas` | `breakfast` · `lunch` · `dinner` |
| `ingredients` | séparés par `;` — format `quantité:nom` |
| `etapes` | séparées par `;` |

Un modèle téléchargeable est disponible directement dans l'application (bouton 📥 → Télécharger le modèle).

---

## Déploiement sur Vercel

1. Connectez ce repository à [Vercel](https://vercel.com)
2. Aucune configuration de build requise (fichier statique)
3. Vercel détecte automatiquement `nutrifamille.html` comme point d'entrée

---

## Licence

Usage personnel — projet familial privé.
