# Istruzioni per Claude — copia personale di Utility360

Copia questo testo all'inizio della conversazione con Claude quando vuoi modificare la tua copia di Utility360.

---

Sto lavorando sulla mia copia di **Utility360**, una web app su Cloudflare Workers + D1 + R2 (nessuna dipendenza npm).
Il codice originale arriva da un altro sviluppatore attraverso un canale di aggiornamenti cifrati; io posso fare modifiche mie, ma devo continuare a ricevere i suoi aggiornamenti.

**Come funziona il mio repo**
- `main`: quello che è online. Cloudflare pubblica da solo ogni push su `main`.
- `rilasci`: solo le versioni originali, una per commit. **Non modificarlo mai a mano**: lo scrive l'automazione.
- `.github/workflows/aggiorna.yml`: ogni notte controlla soltanto se c'è una versione nuova e, se c'è, apre una Issue "Nuova versione di Utility360 …". L'installazione la avvio io a mano (Actions → Aggiorna Utility360 → Run workflow): mette la versione originale in `rilasci` e poi fa `git merge rilasci` in `main`. Se in `main` ci sono modifiche mie rispetto a `rilasci`, si ferma e le elenca finché non rilancio con "Ho controllato". Se l'unione ha conflitti si ferma, non cambia `main` e segna l'esecuzione come fallita.
- `wrangler.toml` contiene la mia configurazione (ID del database ecc.) e viene riscritto da `wrangler.modello.toml` a ogni versione: le impostazioni si cambiano nelle variabili di GitHub Actions (`ID_DATABASE`, `NOME_WORKER`, `NOME_DATABASE`, `NOME_BUCKET`, `GOOGLE_*`), non nel file.

**Regole per le mie modifiche**
1. Lavora sempre su `main` (o su un ramo che poi unisci in `main`), mai su `rilasci`.
2. Fai modifiche piccole e mirate: meno righe dell'originale tocchi, meno conflitti avrò con gli aggiornamenti. Quando puoi, aggiungi file nuovi invece di riscrivere quelli esistenti.
3. **Database**: lo schema si cambia solo con nuove migrazioni in `migrations/`. Usa numeri alti e un nome riconoscibile (es. `9001_mia_….sql`) per non scontrarti con le migrazioni originali; non modificare mai una migrazione esistente. D1 accetta al massimo 5 SELECT unite con UNION: usa `EXISTS … OR EXISTS …`.
4. Prima di ogni commit esegui `npm test` (Node 22.5 o più recente).
5. Non toccare `.versione` né `wrangler.toml` a mano.

**Prima di un aggiornamento, se ho modifiche mie**
Guarda la versione nuova (`git fetch origin` e `git diff main origin/rilasci` dopo averla scaricata, oppure i file elencati dall'automazione) e dimmi se tocca le stesse parti delle mie modifiche e cosa potrebbe rompersi. Poi rilancio con "Ho controllato".

**Quando l'aggiornamento fallisce per conflitti**
1. `git fetch origin` e `git checkout main`.
2. `git merge origin/rilasci`.
3. Risolvi i conflitti tenendo **entrambe** le cose: la novità dell'aggiornamento e la mia modifica. Se non è chiaro come farle convivere, chiedimelo prima di scegliere.
4. `npm test`, poi commit e push su `main`.
