# Istruzioni per Claude — copia personale di Utility360

Copia il testo qui sotto all'inizio di ogni conversazione in cui chiedi a Claude di modificare la tua app o di aiutarti con un aggiornamento. Claude deve avere accesso al tuo repo GitHub. Sostituisci `<mio-utente>` con il tuo nome utente GitHub.

---

Sto lavorando sulla mia copia di Utility360, una web app su Cloudflare Workers + D1 + R2, senza dipendenze npm. Il mio repo GitHub è <mio-utente>/utility360.
Il codice originale arriva da un altro sviluppatore, cifrato, dal repo pubblico axellero90/utility360-aggiornamenti. Posso fare modifiche mie, ma devo continuare a ricevere i suoi aggiornamenti.

COME FUNZIONA IL MIO REPO
- main: quello che è online. Cloudflare pubblica da solo ogni push su main.
- rilasci: solo le versioni originali, una per commit. Non modificarlo mai: lo scrive l'automazione.
- .github/workflows/aggiorna.yml: ogni mattina controlla se c'è una versione nuova e apre una Issue. L'installazione la avvio io (Run workflow): mette la versione originale in rilasci e fa git merge rilasci in main. Se in main ci sono mie modifiche, la versione nuova arriva in rilasci ma non viene unita finché non rilancio con "Ho controllato". Se il merge ha conflitti si ferma senza cambiare main.
- wrangler.toml viene riscritto da wrangler.modello.toml a ogni versione: le impostazioni si cambiano nelle variabili di GitHub Actions (ID_DATABASE, NOME_WORKER, NOME_DATABASE, NOME_BUCKET, GOOGLE_*), mai nel file. Non toccare nemmeno .versione.

REGOLE PER LE MIE MODIFICHE
1. Lavora su main (o su un ramo da unire in main), mai su rilasci.
2. Modifiche piccole e mirate: meno righe originali tocchi, meno conflitti avrò. Quando puoi, aggiungi file nuovi invece di riscrivere quelli esistenti.
3. Database: lo schema si cambia solo con nuove migrazioni in migrations/, con numeri alti e nome riconoscibile (es. 9001_mia_nome.sql). Non modificare mai una migrazione esistente. D1 accetta al massimo 5 SELECT unite con UNION: usa EXISTS ... OR EXISTS ....
4. Prima di ogni commit esegui npm test (Node 22.5 o più recente). Per le modifiche grafiche mostrami uno screenshot.
5. Non fare push su main senza la mia conferma.

PRIMA DI UN AGGIORNAMENTO, SE HO MODIFICHE MIE
Il primo Run si ferma ma mette già la versione nuova nel ramo rilasci. Fai git fetch origin e guarda git diff main origin/rilasci, soprattutto sui file che ho modificato (li elenca il Run). Dimmi se l'aggiornamento tocca le stesse parti e cosa potrebbe rompersi. Poi rilancio io con "Ho controllato".

QUANDO L'AGGIORNAMENTO SI FERMA PER CONFLITTI
1. git fetch origin, git checkout main, git merge origin/rilasci.
2. Risolvi i conflitti tenendo entrambe le cose: la novità dell'aggiornamento e la mia modifica. Se non è chiaro come farle convivere, chiedimelo prima di scegliere.
3. npm test, poi mostrami cosa hai fatto e fai il push su main solo quando confermo.
