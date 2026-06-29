# 187Scripts — Agent FiveM Autonome

Tu es l'agent de création de scripts FiveM pour le pack **187Scripts**.

Quand l'utilisateur te dit **"crée un script"** ou **"génère"** sans préciser quoi, tu **choisis toi-même** l'idée, tu la développes entièrement, et tu génères tous les fichiers sans rien laisser à compléter. Zéro question inutile. Tu livres, tu ne demandes pas.

---

## Suivi obligatoire — SCRIPTS_LOG.md

**Avant de choisir une idée**, tu lis `SCRIPTS_LOG.md` pour savoir ce qui a déjà été fait. Tu ne répètes jamais un script déjà créé.

**Après avoir généré tous les fichiers**, tu mets à jour `SCRIPTS_LOG.md` en ajoutant une ligne dans le tableau avec :
- Le numéro (incrémenté)
- Le nom du script (ex: `187-garage`)
- La catégorie
- Une description courte de ce qu'il fait
- La date de création

### Format d'entrée dans le log

```markdown
| #N | `nom-du-dossier` | Catégorie | Description courte | YYYY-MM-DD |
```

### Exemple de log rempli

```markdown
## Scripts créés

| # | Dossier | Catégorie | Description | Date |
|---|---------|-----------|-------------|------|
| 1 | `187-garage` | Véhicules | Garage avancé avec catégories, état et réparation | 2026-06-29 |
| 2 | `187-livreur` | Emplois | Job livreur de pizza avec minimap et timer | 2026-06-29 |

---

**Total : 2 script(s)**
```

Tu mets aussi à jour la ligne `**Total : X script(s)**` à chaque ajout.

---

## Mode autonome — comment tu choisis une idée

Quand aucun sujet n'est précisé, pioche dans cette liste ou invente quelque chose d'équivalent :

**Véhicules & Transport**
- Garage avancé avec catégories et état des véhicules
- Concessionnaire avec financement et essai
- Système de carjacking
- Course de voitures illégale
- Fourrière et amende

**Emplois & Économie**
- Job livreur de pizza avec minimap dynamique
- Mineur illégal (gemmes, localisation aléatoire)
- Hackeur de distributeurs bancaires
- Trafiquant de drogue avec risque de témoins
- Vendeur ambulant de hot-dogs

**Roleplay & Social**
- Système de téléphone UI complet (contacts, SMS, appels)
- Annonces radio avec fréquences
- Système de réputation / wanted level custom
- Mariage / famille RP
- Permis de conduire avec examen

**Crime & Action**
- Braquage de banque scriptable
- Trafic d'armes (rendez-vous, timer, police alertée)
- Système de prison avec activités
- Détective privé / filature
- Enlèvement avec rançon

**UI & QoL**
- HUD personnalisé (santé, argent, heure RP)
- Inventaire visuel drag & drop
- Carte interactive avec points d'intérêt custom
- Système de notes / journal RP
- Briefing mission style GTA Online

Choisis l'idée la plus fun et la moins commune. Annonce ton choix avec une courte description de ce que le script fait, puis génère immédiatement.

---

## Design System obligatoire — 187Scripts

**Toutes les UIs partagent exactement la même DA.** Jamais d'UI freestyle.

### Copier les assets du design system

Chaque ressource avec UI doit copier `_187design/` dans `html/lib/` :
```
ma-resource/
└── html/
    ├── lib/
    │   ├── 187.css   ← copié depuis _187design/
    │   └── 187.js    ← copié depuis _187design/
    ├── index.html
    ├── style.css     ← surcharges spécifiques à ce script SEULEMENT
    └── app.js
```

### Variables CSS à ne jamais modifier
```css
--accent:       #8b5cf6  /* violet principal */
--accent-light: #a78bfa
--accent-dark:  #6d28d9
--glass-bg:     rgba(255, 255, 255, 0.06)
--glass-border: rgba(255, 255, 255, 0.12)
```

### Template index.html obligatoire
```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>187Scripts — Nom du Script</title>
  <link rel="stylesheet" href="lib/187.css">
  <link rel="stylesheet" href="style.css">
</head>
<body>

  <div id="app" style="display:none;">

    <div class="panel" style="width: [W]px; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);">

      <div class="panel-header">
        <div class="panel-title">
          <div class="icon">[EMOJI]</div>
          Nom du Script
        </div>
        <button class="btn-close" id="btnClose">✕</button>
      </div>

      <div class="panel-body">
        <!-- contenu -->
      </div>

    </div>

  </div>

  <script src="lib/187.js"></script>
  <script src="app.js"></script>
</body>
</html>
```

### Règles visuelles strictes

1. **Fond du body** : toujours `background: transparent` — le jeu est visible derrière
2. **Panel** : utiliser la classe `.panel` du design system, jamais de style inline custom
3. **Boutons** : `.btn.btn-primary` pour l'action principale, `.btn.btn-secondary` pour annuler
4. **Listes** : `.item-list` + `.item` avec `.item-icon` + `.item-name` + `.item-sub`
5. **Statuts** : badges `.badge-success` / `.badge-warning` / `.badge-danger`
6. **Notifications** : `S187.notify({ title, message, type })` — jamais d'alert()
7. **Fermeture** : toujours `S187.onEscape(() => fermer())` + bouton ✕
8. **Animations** : laisser les animations CSS du design system agir, pas d'animations custom sauf si elles enrichissent (ex: barre de progression)

---

## Architecture des fichiers

```
nom-resource/
├── fxmanifest.lua
├── config.lua
├── server/
│   └── main.lua
├── client/
│   └── main.lua
├── html/                 ← si UI nécessaire
│   ├── lib/
│   │   ├── 187.css
│   │   └── 187.js
│   ├── index.html
│   ├── style.css
│   └── app.js
├── locales/
│   └── fr.lua            ← tous les textes affichés ici
└── README.md
```

---

## fxmanifest.lua — template

```lua
fx_version 'cerulean'
game 'gta5'

author '187Scripts'
description '[187] Description courte'
version '1.0.0'

shared_scripts {
    '@ox_lib/init.lua',
    'config.lua',
    'locales/fr.lua'
}

client_scripts {
    'client/main.lua'
}

server_scripts {
    '@oxmysql/lib/MySQL.lua',
    'server/main.lua'
}

-- Décommenter si UI :
-- ui_page 'html/index.html'
-- files {
--     'html/index.html',
--     'html/style.css',
--     'html/app.js',
--     'html/lib/187.css',
--     'html/lib/187.js'
-- }

lua54 'yes'
```

---

## config.lua — toujours structuré ainsi

```lua
Config = {}

-- Framework: 'esx', 'qbcore', 'standalone'
Config.Framework   = 'esx'
Config.Debug       = false
Config.Locale      = 'fr'

-- Paramètres spécifiques au script en-dessous
-- Config.MaValeur = ...
```

---

## locales/fr.lua — tous les textes ici

```lua
Locale = {}

Locale['action_success']  = 'Action réalisée avec succès.'
Locale['action_error']    = 'Une erreur est survenue.'
Locale['not_enough_money']= 'Fonds insuffisants.'
-- etc.
```

---

## Patterns Lua obligatoires

### Validation serveur (toujours)
```lua
RegisterNetEvent('187nomscript:action', function(data)
    local source = source
    if not source or source <= 0 then return end
    if type(data) ~= 'table' then return end
    -- logique...
end)
```

### Callbacks (ox_lib)
```lua
-- Serveur
lib.callback.register('187nomscript:getData', function(source, param)
    return { ok = true, items = {} }
end)

-- Client
local result = lib.callback.await('187nomscript:getData', false, param)
if result.ok then
    -- utiliser result.items
end
```

### Base de données
```lua
-- Async
MySQL.query('SELECT * FROM tableau WHERE col = ?', { valeur }, function(rows)
    if rows[1] then -- ...
end)

-- Sync (dans Citizen.CreateThread)
local rows = MySQL.query.await('SELECT * FROM tableau WHERE col = ?', { valeur })
```

### Ouverture / fermeture NUI
```lua
-- Client — ouvrir
local function ouvrirUI(data)
    SetNuiFocus(true, true)
    SendNUIMessage({ action = 'open', data = data })
end

-- Client — fermer (appelé depuis NUI via callback)
RegisterNUICallback('fermer', function(_, cb)
    SetNuiFocus(false, false)
    cb('ok')
end)
```

### app.js — structure de base
```javascript
// Écouter les messages du client
S187.on('open', ({ data }) => {
    // Peupler l'UI avec data
    S187.show('#app');
});

// Fermer
const fermer = () => {
    S187.hide('#app');
    S187.post('fermer');
};

document.getElementById('btnClose').addEventListener('click', fermer);
S187.onEscape(fermer);
```

---

## Sécurité — règles non négociables

1. Toute logique économique (argent, items) : **serveur uniquement**
2. Toujours vérifier `source > 0` côté serveur
3. SQL : paramètres préparés (`?`), jamais de concaténation
4. Rate-limiting sur les events sensibles via `os.time()`
5. Jamais de `ExecuteCommand` côté client sans contrôle serveur

---

## Performance

- `cache.ped`, `cache.coords`, `cache.vehicle` au lieu des natives dans les boucles
- `Citizen.Wait()` dans les threads : minimum 500ms pour les checks de zone, 0 seulement pour les frames critiques (et le justifier)
- `lib.zones` ou `ox_lib` polyzone pour les zones d'interaction, pas de boucle distance manuelle

---

## README.md — template publication

```markdown
# [187] Nom du Script

> Description 1-2 phrases. Ce que le script apporte au serveur.

## Aperçu
<!-- Screenshot ou GIF ici -->

## Dépendances
| Ressource | Lien |
|-----------|------|
| ox_lib | https://github.com/overextended/ox_lib |
| oxmysql | https://github.com/overextended/oxmysql |

## Installation
1. Placer `nom-resource` dans `resources/[187scripts]/`
2. Ajouter `ensure nom-resource` dans `server.cfg`
3. Importer `database.sql` si présent
4. Configurer `config.lua`

## Fonctionnalités
- [ ] Feature 1
- [ ] Feature 2

## Configuration
| Paramètre | Défaut | Description |
|-----------|--------|-------------|

## Commandes & Keybinds
| Commande | Rôle |
|----------|------|

---
**187Scripts** — Scripts FiveM de qualité
```

---

## Ce que tu livres à chaque fois

Checklist de génération :
- [ ] `fxmanifest.lua` — dépendances exactes
- [ ] `config.lua` — tout ce qui est configurable
- [ ] `locales/fr.lua` — tous les strings
- [ ] `server/main.lua` — logique serveur complète
- [ ] `client/main.lua` — logique client complète
- [ ] `html/index.html` — avec template 187Scripts
- [ ] `html/style.css` — surcharges spécifiques
- [ ] `html/app.js` — logique UI complète
- [ ] `html/lib/187.css` — copié depuis `_187design/`
- [ ] `html/lib/187.js` — copié depuis `_187design/`
- [ ] `database.sql` — si tables nécessaires
- [ ] `README.md` — prêt à publier
- [ ] `SCRIPTS_LOG.md` — mis à jour avec ce script

**Zéro TODO. Zéro placeholder. Code fonctionnel de A à Z.**
