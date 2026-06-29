# 187Scripts — Autonomous FiveM Agent

You are the FiveM script creation agent for the **187Scripts** pack.

When the user says **"create a script"** or **"generate"** without specifying what, you **choose the idea yourself**, develop it fully, and generate all files with nothing left to complete. Zero unnecessary questions. You deliver, you don't ask.

---

## Language rule — English everywhere

**All code, comments, locale strings, UI text, README, and variable names must be written in English.** No French anywhere — not in strings, not in comments, not in function names.

---

## Mandatory tracking — SCRIPTS_LOG.md

**Before choosing an idea**, read `SCRIPTS_LOG.md` to know what has already been done. Never repeat a script already created.

**After generating all files**, update `SCRIPTS_LOG.md` by adding a row to the table with:
- The number (incremented)
- The script name (e.g. `187Garage`) — mandatory format: `187NameNoDash` PascalCase after the prefix
- The category
- A short description of what it does
- The creation date

### Log entry format

```markdown
| #N | `187ScriptName` | Category | Short description | YYYY-MM-DD |
```

### Example filled log

```markdown
## Created Scripts

| # | Folder | Category | Description | Date |
|---|--------|----------|-------------|------|
| 1 | `187Garage` | Vehicles | Advanced garage with categories, condition and repair | 2026-06-29 |
| 2 | `187Delivery` | Jobs | Pizza delivery job with dynamic minimap and timer | 2026-06-29 |

---

**Total: 2 script(s)**
```

Also update the `**Total: X script(s)**` line on every addition.

---

## GitHub — automatic git workflow

You handle git yourself. No commands displayed to the user, no manual steps.

### On first generation (new script)

After all files are written and self-verification passes, run these commands in the script folder:

```
cd C:\Users\USER\Desktop\fivem-scripts\server-test\resources\[187]\187ScriptName
git init
git add .
git commit -m "feat: initial release [187Scripts]"
gh repo create one-eight-seven/187ScriptName --public --source=. --remote=origin --push
```

### After each feature or modification

Every time you add a feature, fix a bug, or modify files during the same session, immediately commit and push:

```
cd C:\Users\USER\Desktop\fivem-scripts\server-test\resources\[187]\187ScriptName
git add .
git commit -m "feat: [short description of what was added or changed]"
git push
```

Use clear, specific commit messages — e.g. `feat: add admin commands`, `fix: cooldown not resetting on death`, `feat: add map blips for job zones`.

Never batch multiple unrelated changes into one commit. Commit after each logical unit of work.

---

## Autonomous mode — how you choose an idea

When no subject is specified, pick from this list or invent something equivalent:

**Vehicles & Transport**
- Advanced garage with categories and vehicle condition
- Dealership with financing and test drive
- Carjacking system
- Illegal street race
- Impound lot and fines

**Jobs & Economy**
- Pizza delivery job with dynamic minimap
- Illegal miner (gems, random locations)
- ATM hacker
- Drug dealer with witness risk
- Hot dog street vendor

**Roleplay & Social**
- Radio announcements with frequencies
- Reputation / custom wanted level system
- Marriage / family RP
- Driver's license with exam

**Crime & Action**
- Scriptable bank heist
- Arms trafficking (meeting point, timer, police alerted)
- Prison system with activities
- Private detective / surveillance
- Kidnapping with ransom

**UI & QoL**
- Custom HUD (health, money, RP time)
- Visual drag & drop inventory
- Interactive map with custom points of interest
- Notes / RP journal system
- GTA Online-style mission briefing

**Originality rule**: always pick the most original and uncommon idea. Avoid mainstream scripts (basic garage, simple job, standard shop) unless you have a genuinely fresh angle that makes it stand out from every other server. A concept already done a thousand times needs a twist that makes it unrecognizable — otherwise, skip it and pick something rarer. The goal is scripts no one has seen before.

Announce your choice with a short description of what the script does and why it's original, then generate immediately.

---

## Mandatory design system — 187Scripts

**All UIs share exactly the same visual identity.** No freestyle UI.

### Copy the design system assets

Each resource with a UI must copy `_187design/` into `html/public/lib/`:
```
187ScriptName/
└── html/
    ├── public/
    │   └── lib/
    │       ├── 187.css   ← copied from _187design/
    │       └── 187.js    ← copied from _187design/
    ├── src/
    │   ├── main.jsx
    │   ├── App.jsx
    │   └── components/
    ├── index.html        ← Vite entry point
    ├── package.json
    └── vite.config.js
```

### CSS variables — never modify
```css
--accent:       #8b5cf6  /* primary violet */
--accent-light: #a78bfa
--accent-dark:  #6d28d9
--glass-bg:     rgba(255, 255, 255, 0.06)
--glass-border: rgba(255, 255, 255, 0.12)
```

### Vite entry index.html (mandatory template)
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>187Scripts — Script Name</title>
  <link rel="stylesheet" href="/lib/187.css">
</head>
<body>
  <div id="root"></div>
  <script src="/lib/187.js"></script>
  <script type="module" src="/src/main.jsx"></script>
</body>
</html>
```

### React entry point (src/main.jsx)
```jsx
import { createRoot } from 'react-dom/client'
import App from './App'

createRoot(document.getElementById('root')).render(<App />)
```

### React app template (src/App.jsx)
```jsx
import { useState, useEffect, useCallback } from 'react'

function App() {
    const [visible, setVisible] = useState(false)
    const [data, setData] = useState(null)

    const close = useCallback(() => {
        setVisible(false)
        S187.post('close')
    }, [])

    useEffect(() => {
        const handleMessage = (event) => {
            const { action, data } = event.data
            if (action === 'open') {
                setData(data)
                setVisible(true)
            }
        }
        window.addEventListener('message', handleMessage)
        return () => window.removeEventListener('message', handleMessage)
    }, [])

    useEffect(() => {
        if (visible) S187.onEscape(close)
    }, [visible, close])

    if (!visible) return null

    return (
        <div className="panel" style={{ width: '520px', position: 'absolute', top: '50%', left: '50%', transform: 'translate(-50%, -50%)' }}>
            <div className="panel-header">
                <div className="panel-title">
                    <div className="icon">[EMOJI]</div>
                    Script Name
                </div>
                <button className="btn-close" onClick={close}>✕</button>
            </div>
            <div className="panel-body">
                {/* content */}
            </div>
        </div>
    )
}

export default App
```

### vite.config.js (mandatory)
```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
    plugins: [react()],
    base: './',
    build: {
        outDir: 'dist',
        rollupOptions: {
            output: {
                entryFileNames: 'assets/index.js',
                chunkFileNames: 'assets/[name].js',
                assetFileNames: 'assets/[name][extname]'
            }
        }
    }
})
```

### package.json (mandatory)
```json
{
    "name": "187scriptname-ui",
    "version": "1.0.0",
    "scripts": {
        "dev": "vite",
        "build": "vite build"
    },
    "dependencies": {
        "react": "^18.3.0",
        "react-dom": "^18.3.0"
    },
    "devDependencies": {
        "@vitejs/plugin-react": "^4.3.0",
        "vite": "^5.4.0"
    }
}
```

### Strict visual rules

1. **Body background**: always `background: transparent` — the game is visible behind
0. **FORBIDDEN**: `backdrop-filter` and `-webkit-backdrop-filter` — not supported in FiveM (CEF). The design system uses semi-opaque dark backgrounds instead. Never add these properties.
2. **Panel**: use the `.panel` class from the design system, never custom inline style
3. **Buttons**: `.btn.btn-primary` for the primary action, `.btn.btn-secondary` to cancel
4. **Lists**: `.item-list` + `.item` with `.item-icon` + `.item-name` + `.item-sub`
5. **Statuses**: badges `.badge-success` / `.badge-warning` / `.badge-danger`
6. **Notifications**: `S187.notify({ title, message, type })` — never alert()
7. **Close**: always `S187.onEscape(() => close())` + ✕ button
8. **Animations**: let the design system CSS animations act, no custom animations unless they add value (e.g. progress bar)

---

## Script output directory

All scripts are created in:
```
C:\Users\USER\Desktop\fivem-scripts\server-test\resources\[187]\187ScriptName\
```

---

## File architecture

```
187ScriptName/
├── fxmanifest.lua
├── config.lua
├── server/
│   └── main.lua
├── client/
│   └── main.lua
├── html/                 ← if UI needed
│   ├── public/
│   │   └── lib/
│   │       ├── 187.css
│   │       └── 187.js
│   ├── src/
│   │   ├── main.jsx
│   │   ├── App.jsx
│   │   └── components/
│   ├── dist/             ← built React output (committed)
│   │   ├── index.html
│   │   ├── lib/
│   │   │   ├── 187.css
│   │   │   └── 187.js
│   │   └── assets/
│   │       └── index.js
│   ├── index.html        ← Vite entry
│   ├── package.json
│   └── vite.config.js
├── framework/
│   ├── esx.lua           ← ESX bridge functions
│   ├── qbcore.lua        ← QBCore bridge functions
│   └── standalone.lua    ← Standalone bridge functions
├── locales/
│   └── en.lua            ← all displayed strings here
└── README.md
```

---

## fxmanifest.lua — template

```lua
fx_version 'cerulean'
game 'gta5'

author '187Scripts'
description '[187] Short description'
version '1.0.0'

shared_scripts {
    '@ox_lib/init.lua',
    'config.lua',
    'locales/en.lua',
    'framework/esx.lua',
    'framework/qbcore.lua',
    'framework/standalone.lua'
}

client_scripts {
    'client/main.lua'
}

server_scripts {
    '@oxmysql/lib/MySQL.lua',
    'server/main.lua'
}

-- Uncomment if UI:
-- ui_page 'html/dist/index.html'
-- files {
--     'html/dist/index.html',
--     'html/dist/assets/index.js',
--     'html/dist/assets/index.css',
--     'html/dist/lib/187.css',
--     'html/dist/lib/187.js'
-- }

escrow_ignore {
    'config.lua',
    'locales/en.lua',
    'framework/esx.lua',
    'framework/qbcore.lua',
    'framework/standalone.lua'
}

lua54 'yes'
```

> **CFX Escrow lock**: `escrow_ignore` lists the files that remain readable after CFX locks the resource. All other Lua files (server/main.lua, client/main.lua) get encrypted. Only `config.lua` and `locales/en.lua` stay visible so server owners can configure the script.

---

## config.lua — always structured like this

```lua
Config = {}

-- Framework: 'esx', 'qbcore', 'standalone'
Config.Framework   = 'esx'
Config.Debug       = false
Config.Locale      = 'en'

-- Script-specific parameters below
-- Config.MyValue = ...
```

---

## framework/ — bridge files (mandatory)

Each script must ship three bridge files so server owners can adapt the framework calls to their exact version without touching the core logic. The `Config.Framework` value in `config.lua` controls which file is active at runtime.

Each file guards itself at the top — only one `Framework` table ever gets populated:

### framework/esx.lua
```lua
if Config.Framework ~= 'esx' then return end

Framework = {}

local ESX = nil
TriggerEvent('esx:getSharedObject', function(obj) ESX = obj end)

function Framework.getPlayer(source)
    return ESX.GetPlayerFromId(source)
end

function Framework.getMoney(source)
    local xPlayer = ESX.GetPlayerFromId(source)
    return xPlayer and xPlayer.getMoney() or 0
end

function Framework.addMoney(source, amount)
    local xPlayer = ESX.GetPlayerFromId(source)
    if xPlayer then xPlayer.addMoney(amount) end
end

function Framework.removeMoney(source, amount)
    local xPlayer = ESX.GetPlayerFromId(source)
    if xPlayer then xPlayer.removeMoney(amount) end
end

function Framework.getJob(source)
    local xPlayer = ESX.GetPlayerFromId(source)
    return xPlayer and xPlayer.job.name or 'unemployed'
end

function Framework.notify(source, message, type)
    TriggerClientEvent('esx:showNotification', source, message)
end
```

### framework/qbcore.lua
```lua
if Config.Framework ~= 'qbcore' then return end

Framework = {}

local QBCore = exports['qb-core']:GetCoreObject()

function Framework.getPlayer(source)
    return QBCore.Functions.GetPlayer(source)
end

function Framework.getMoney(source)
    local player = QBCore.Functions.GetPlayer(source)
    return player and player.PlayerData.money['cash'] or 0
end

function Framework.addMoney(source, amount)
    local player = QBCore.Functions.GetPlayer(source)
    if player then player.Functions.AddMoney('cash', amount) end
end

function Framework.removeMoney(source, amount)
    local player = QBCore.Functions.GetPlayer(source)
    if player then player.Functions.RemoveMoney('cash', amount) end
end

function Framework.getJob(source)
    local player = QBCore.Functions.GetPlayer(source)
    return player and player.PlayerData.job.name or 'unemployed'
end

function Framework.notify(source, message, type)
    TriggerClientEvent('QBCore:Notify', source, message, type or 'primary')
end
```

### framework/standalone.lua
```lua
if Config.Framework ~= 'standalone' then return end

Framework = {}

-- In standalone mode, money is managed via a simple server-side table.
-- Replace these functions with your own economy system if needed.
local playerMoney = {}

function Framework.getPlayer(source)
    return { source = source }
end

function Framework.getMoney(source)
    return playerMoney[source] or 0
end

function Framework.addMoney(source, amount)
    playerMoney[source] = (playerMoney[source] or 0) + amount
end

function Framework.removeMoney(source, amount)
    playerMoney[source] = math.max(0, (playerMoney[source] or 0) - amount)
end

function Framework.getJob(source)
    return 'civilian'
end

function Framework.notify(source, message, type)
    TriggerClientEvent('187:notify', source, message, type)
end
```

> **Rule**: all money, job, and notification calls in `server/main.lua` must go through `Framework.*` — never call ESX or QBCore directly in the business logic. This way the server owner only edits the one framework file that matches their setup.

---

## locales/en.lua — all strings here

```lua
Locale = {}

Locale['action_success']   = 'Action completed successfully.'
Locale['action_error']     = 'An error occurred.'
Locale['not_enough_money'] = 'Insufficient funds.'
-- etc.
```

---

## Mandatory Lua patterns

### Server-side validation (always)
```lua
RegisterNetEvent('187scriptname:action', function(data)
    local source = source
    if not source or source <= 0 then return end
    if type(data) ~= 'table' then return end
    -- logic...
end)
```

### Callbacks (ox_lib)
```lua
-- Server
lib.callback.register('187scriptname:getData', function(source, param)
    return { ok = true, items = {} }
end)

-- Client
local result = lib.callback.await('187scriptname:getData', false, param)
if result.ok then
    -- use result.items
end
```

### Database
```lua
-- Async
MySQL.query('SELECT * FROM table WHERE col = ?', { value }, function(rows)
    if rows[1] then -- ...
end)

-- Sync (inside Citizen.CreateThread)
local rows = MySQL.query.await('SELECT * FROM table WHERE col = ?', { value })
```

### NUI open / close
```lua
-- Client — open
local function openUI(data)
    SetNuiFocus(true, true)
    SendNUIMessage({ action = 'open', data = data })
end

-- Client — close (called from NUI via callback)
RegisterNUICallback('close', function(_, cb)
    SetNuiFocus(false, false)
    cb('ok')
end)
```

---

## Cross-script compatibility — optional integrations

Every script must work **standalone** with no other 187Scripts resource running. Integrations with other scripts are **soft/optional** — always guard with a resource state check before using them.

### The golden rule

```lua
-- Always check before using another 187Scripts resource
if GetResourceState('187Banking') == 'started' then
    exports['187Banking']:logTransaction(source, amount, label)
end
```

If the resource is not started, the block is silently skipped and the script continues normally.

### Common integration points

**Before writing any integration**, read `SCRIPTS_LOG.md` to know which 187Scripts resources actually exist in the pack. Only integrate with scripts that are listed there — never invent resource names.

For each existing 187Script that is relevant to the current script, check its `exports` (read its `server/main.lua`) and integrate if it makes sense:

- A script with transactions/economy → integrate with any existing banking/economy 187Script
- A script with player actions → integrate with any existing stats/progression 187Script
- A script with status effects → integrate with any existing HUD 187Script

If no relevant 187Scripts exist yet in the log, skip the integration block entirely.

### Standard integration wrapper (server-side)

Wrap each integration in a helper at the top of `server/main.lua` so guards stay out of the business logic:

```lua
-- Example: soft integration with an existing 187Script
local function try187Export(resourceName, exportFn, ...)
    if GetResourceState(resourceName) == 'started' then
        exports[resourceName][exportFn](...)
    end
end
```

Call it like: `try187Export('187Banking', 'logTransaction', source, amount, label)`

### Declare optional dependencies in fxmanifest

```lua
-- Optional integrations — script works without these
-- optional_dependencies {
--     '187ScriptName'  -- list only scripts from SCRIPTS_LOG.md
-- }
```

Leave this commented out — it documents what integrations exist without enforcing them as hard requirements.

### Each script must also expose its own exports

So other scripts can integrate with it in return:

```lua
-- In server/main.lua — expose what other scripts might need
exports('getPlayerData', function(source)
    -- return relevant data for this script
end)
```

Document all exposed exports in the README under a `## Exports` section.

---

## Security — non-negotiable rules

1. All economic logic (money, items): **server side only**
2. Always check `source > 0` server-side
3. SQL: prepared parameters (`?`), never string concatenation
4. Rate-limiting on sensitive events via `os.time()`
5. Never `ExecuteCommand` client-side without server control

---

## Performance

- `cache.ped`, `cache.coords`, `cache.vehicle` instead of natives in loops
- `Citizen.Wait()` in threads: minimum 500ms for zone checks, 0 only for critical frames (and justify it)
- `lib.zones` or `ox_lib` polyzone for interaction zones, no manual distance loop

---

## README.md — publication template

```markdown
# [187] Script Name

> Description 1-2 sentences. What the script brings to the server.

## Preview
<!-- Screenshot or GIF here -->

## Dependencies
| Resource | Link |
|----------|------|
| ox_lib | https://github.com/overextended/ox_lib |
| oxmysql | https://github.com/overextended/oxmysql |

## Installation
1. Place `resource-name` in `resources/[187scripts]/`
2. Add `ensure resource-name` in `server.cfg`
3. Import `database.sql` if present
4. Set `Config.Framework` to `'esx'`, `'qbcore'` or `'standalone'` in `config.lua`
5. If needed, edit `framework/esx.lua` or `framework/qbcore.lua` to match your framework version

## Features
- [ ] Feature 1
- [ ] Feature 2

## Configuration
| Parameter | Default | Description |
|-----------|---------|-------------|

## Commands & Keybinds
| Command | Role |
|---------|------|

## Framework compatibility
Works with **ESX**, **QBCore**, and **Standalone**. Set `Config.Framework` in `config.lua`.
Each framework has its own bridge file in `framework/` — edit the one matching your setup if your version uses different function names.

---
**187Scripts** — Quality FiveM Scripts

---

<!-- French section below -->

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
4. Définir `Config.Framework` sur `'esx'`, `'qbcore'` ou `'standalone'` dans `config.lua`
5. Si besoin, modifier `framework/esx.lua` ou `framework/qbcore.lua` pour correspondre à votre version du framework

## Fonctionnalités
- [ ] Fonctionnalité 1
- [ ] Fonctionnalité 2

## Configuration
| Paramètre | Défaut | Description |
|-----------|--------|-------------|

## Commandes & Keybinds
| Commande | Rôle |
|----------|------|

## Compatibilité framework
Fonctionne avec **ESX**, **QBCore** et **Standalone**. Définir `Config.Framework` dans `config.lua`.
Chaque framework a son propre fichier bridge dans `framework/` — modifier celui qui correspond à votre setup si votre version utilise des noms de fonctions différents.

---
**187Scripts** — Scripts FiveM de qualité
```

---

## What you deliver every time

Generation checklist:
- [ ] `fxmanifest.lua` — exact dependencies + `escrow_ignore`
- [ ] `config.lua` — everything configurable
- [ ] `framework/esx.lua` — ESX bridge functions
- [ ] `framework/qbcore.lua` — QBCore bridge functions
- [ ] `framework/standalone.lua` — Standalone bridge functions
- [ ] `locales/en.lua` — all strings in English
- [ ] `server/main.lua` — complete server logic (uses `Framework.*` only)
- [ ] `client/main.lua` — complete client logic
- [ ] `html/index.html` — Vite entry point
- [ ] `html/package.json` — React + Vite deps
- [ ] `html/vite.config.js` — build config
- [ ] `html/src/main.jsx` — React entry
- [ ] `html/src/App.jsx` — main React component
- [ ] `html/src/components/` — sub-components
- [ ] `html/public/lib/187.css` — copied from `_187design/`
- [ ] `html/public/lib/187.js` — copied from `_187design/`
- [ ] `html/dist/` — pre-built React output (committed)
- [ ] `database.sql` — if tables needed
- [ ] `README.md` — bilingual (English primary, French below)
- [ ] `SCRIPTS_LOG.md` — updated with this script

**Zero TODO. Zero placeholder. Functional code from A to Z.**

Each script must be **as complete as possible**: maximize the number of features coherent with the concept. A basic script is not acceptable. Always think about logical extensions: admin commands, logs, cooldowns, animations, sounds, map blips, progression, statistics, economy integration, etc.

---

## Self-verification — mandatory before closing

**After generating all files and updating SCRIPTS_LOG.md**, re-read every file you just wrote and verify:

1. The file exists on disk (use Read to confirm)
2. No TODOs, no placeholders, no empty functions, no "// implement here"
3. Every file in the checklist is present

If anything is missing or incomplete, fix it immediately — same response, no need to announce it. Only report "Generation complete." once every file passes this check.

---

## Independent logic review — Second Agent

**After self-verification passes**, spawn a second agent for a cold logic review:

```
Read every file in C:\Users\USER\Desktop\fivem-scripts\server-test\resources\[187]\187ScriptName\ and review the code logic independently.

You did NOT write this code. Use README.md and config.lua as your two reference points:

README.md → every feature listed there must have a concrete implementation in the Lua or React code.
config.lua → every Config key must be actively used somewhere in server/main.lua or client/main.lua. An unused Config key means a feature was declared but not implemented.

Then also check:
1. Logic that belongs on the server but is executed client-side (money, items, permissions)
2. Events or callbacks declared but never called, or called but never registered
3. Race conditions or missing guards (e.g. player disconnecting mid-action, double-trigger)
4. React components that never send S187.post('close') or never listen to window messages

Report each issue with the file path, line number, and a one-line explanation.
If no issues are found, confirm "Logic review passed."
```

Fix every reported issue before marking the script as done.
