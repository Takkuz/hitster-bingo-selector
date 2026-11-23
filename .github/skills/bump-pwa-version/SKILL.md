---
name: bump-pwa-version
description: MUST be used after ANY change to the app files (index.html, sw.js, manifest.webmanifest, icons or any new asset). Reminds to bump the service worker cache version in sw.js so installed PWAs download the update instead of serving the stale cached copy.
---

# Aggiornare la versione della PWA

L'app è una PWA: [sw.js](../../../sw.js) salva in cache i file elencati in `ASSETS` dentro una cache chiamata con la versione:

```js
const CACHE = 'hitster-bingo-v1';
```

Il service worker usa la cache per tutto tranne la pagina. Se un file cambia ma `CACHE` resta uguale, i telefoni che hanno già installato l'app **continuano a usare la versione vecchia** di icone e manifest, e il vecchio service worker resta attivo.

## Regola

Ad **ogni modifica** di uno qualsiasi di questi file, incrementa di 1 il numero in `CACHE` dentro `sw.js`, nella stessa modifica:

- `index.html` (HTML, CSS, JS, categorie, icone incorporate)
- `sw.js` stesso
- `manifest.webmanifest`
- `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png`
- qualsiasi nuovo file servito dall'app

Basta **un solo incremento per ogni sessione o rilascio**, anche se si modificano più file.

## Procedura

1. Leggi la versione attuale: cerca `const CACHE =` in `sw.js`.
2. Incrementa il numero finale: `hitster-bingo-v1` → `hitster-bingo-v2`.
3. Se hai **aggiunto** un nuovo file che deve funzionare offline, aggiungilo anche all'array `ASSETS` (percorso relativo, es. `'./nuovo-file.png'`). Se ne hai **rimosso** uno, toglilo da `ASSETS`: `cache.addAll` fallisce del tutto se anche un solo file restituisce 404.
4. Nel riepilogo all'utente, indica la nuova versione.

## Esempio

Modifica del colore del pulsante in `index.html`:

```diff
- const CACHE = 'hitster-bingo-v3';
+ const CACHE = 'hitster-bingo-v4';
```

❌ Sbagliato: modificare `index.html` lasciando `hitster-bingo-v3`: gli utenti con l'app installata non vedono le modifiche alle icone e il vecchio service worker resta attivo.
✅ Corretto: modificare `index.html` **e** portare `CACHE` a `hitster-bingo-v4` nella stessa modifica.

## Eccezioni

Non serve aggiornare la versione per modifiche a file non serviti dall'app (es. questa skill, README, file in `.github/`).
