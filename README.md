# KHZone

> La cronaca (non ufficiale) del multiverso di Kingdom Hearts — un'enciclopedia generata, non scritta a mano.

## L'idea

KHZone non è un sito su Kingdom Hearts scritto a mano: è un **client** che va a prendere le informazioni da wiki e fonti già esistenti tramite le loro API pubbliche, e le riorganizza in un prodotto più curato, coerente e navigabile di quanto lo siano gli originali.

Niente articoli scritti da zero, niente database di lore copiato a mano: ogni scheda (personaggio, mondo, keyblade, gioco, sviluppatore...) viene **generata** a partire da una richiesta API, con una tua libertà creativa solo su come organizzarla, presentarla e collegarla al resto. Se domani la wiki sorgente aggiorna una pagina, KHZone deve poterla recuperare di nuovo senza che tu debba toccare una riga di contenuto.

Il bello è che è un progetto reale, con tutti i problemi reali: dati che arrivano "sporchi", limiti di frequenza delle chiamate, CORS, licenze da rispettare, cache da progettare. Non un esercizio da corso, un prodotto vero — solo più piccolo.

## Principi guida (non negoziabili)

1. **Niente hardcoding di contenuti "di lore".** Se è un'informazione su Kingdom Hearts (nome, descrizione, immagine, relazioni...), deve arrivare da un'API. I dati finti/placeholder vanno bene solo nelle primissime milestone, prima di avere gli strumenti per fare le chiamate vere — e lo dico esplicitamente milestone per milestone.
2. **Cita sempre la fonte.** I contenuti delle wiki Fandom sono licenziati CC BY-SA: ogni scheda deve linkare all'articolo originale. Non è solo educazione, è la licenza.
3. **Non martellare le API.** User-Agent descrittivo, cache lato server, niente chiamata live ad ogni singola visita di pagina una volta che hai un database a disposizione.
4. **Ogni milestone deve produrre qualcosa che gira**, anche se piccolo e brutto. Niente "lo rifinisco dopo" — quello dopo diventa il debito tecnico della milestone successiva.
5. **Un branch per milestone**, commit piccoli e frequenti con messaggi che spiegano il perché, non il cosa.

## Le fonti dati

| Fonte | Endpoint | Cosa ci prendi | Note |
|---|---|---|---|
| **Kingdom Hearts Wiki** (Fandom) | `https://kingdomhearts.fandom.com/api.php` | Personaggi, mondi, keyblade, nemici, organizzazioni, terminologia | API MediaWiki standard (`action=query`, `action=parse`...). **Non ha CORS**: da browser diretto non funziona, serve un proxy server-side → arriva col modulo PHP. |
| **Wikipedia** | `https://it.wikipedia.org/w/api.php` (o `en.`) | Square Enix, Tetsuya Nomura, storia della saga, piattaforme/console, sviluppatori | Supporta CORS con il parametro `&origin=*` → richiamabile anche direttamente da JS lato client. Buon primo esperimento col modulo DOM/Fetch. |
| **RAWG** *(facoltativo, milestone avanzate)* | `https://api.rawg.io/api/` | Catalogo giochi per piattaforma, copertine, date di uscita | Richiede una API key gratuita → occasione per imparare a gestire secrets con `.env` in Laravel. In alternativa, per chi vuole strafare: **IGDB** (più ricco, ma richiede OAuth via Twitch). |
| **Altre wiki Fandom** *(stretch finale)* | es. `https://finalfantasy.fandom.com/api.php` | Sezione "cameo e crossover" (KH pesca da FF, Disney, ecc.) | Stessa identica API della KH Wiki, cambia solo il sottodominio: quasi a costo zero una volta che il client è generico. |

Prima regola pratica: non fidarti di questa tabella e basta. Ogni wiki MediaWiki espone una sandbox interattiva su `/wiki/Special:ApiSandbox` — usala per esplorare da solo cosa puoi chiedere prima di scrivere codice. Con `action=query&meta=siteinfo&siprop=extensions` scopri anche quali estensioni (es. estrazione di testo semplice) sono attive su quella specifica wiki: non tutte le wiki Fandom hanno le stesse.

Esempio verificato e funzionante (KH Wiki, elenco pagine di una categoria):
```
https://kingdomhearts.fandom.com/api.php?action=query&list=categorymembers&cmtitle=Category:Keyblades&format=json&cmlimit=20
```

## Come è organizzata la roadmap

Ogni milestone corrisponde (più o meno) a un modulo del corso strutturato. La regola d'oro: **costruisci solo con gli strumenti che hai già visto fino a quel punto**. Niente framework prima del tempo, niente scorciatoie prese in prestito da moduli futuri — se in una milestone ti manca uno strumento per fare le cose "come si deve", è voluto: userai dati finti o soluzioni più rozze, e tornerai a sistemarle quando avrai lo strumento giusto. È così che funziona anche nel lavoro vero.

Alla fine di ogni milestone trovi una checklist **"Sei pronto per"**: se sai rispondere di sì a ogni punto, vai ad affrontare i selfwork ufficiali di quel modulo con fiducia — li avrai già praticati, applicati a un problema vero invece che a un esercizio isolato.

---

### Milestone 0 — Setup, terminale, metodo di studio
*(Modulo 1 · Introduzione e Fondamenti — Modulo 5 · Apprendimento e Problem Solving)*

**Obiettivo:** repo pronto, ambiente pronto, metodo di studio rodato.

**Cosa fai:**
- Struttura iniziale del repo: `index.html` vuoto, cartella `/assets`, cartella `/docs`.
- In `/docs/note-fonti.md`, esplora a mano (da browser, senza scrivere codice) 3-4 query diverse sull'API della KH Wiki e annota cosa restituiscono e cosa non ti aspettavi.
- Apri la `Special:ApiSandbox` della KH Wiki e prova almeno un paio di azioni diverse da quella mostrata sopra.
- Carica le lezioni dei Moduli 1 e 5 su NotebookLM e scriviti un tuo mini metodo di studio: come organizzi le sessioni, quando e come usi ChatGPT per capire un concetto senza fartelo risolvere al posto tuo.

**Sei pronto per:** Console Unix, Visual Studio Code, i quiz del Modulo 1.

---

### Milestone 1 — HTML & CSS: la prima pagina statica
*(Modulo 2)*

**Obiettivo:** struttura semantica e stile, zero logica, zero dati veri.

**Cosa fai:**
- Homepage statica con dati **finti e scritti a mano** (è l'unica milestone dove è esplicitamente ok): 4-5 categorie tematiche a scelta tua, diverse dalla struttura standard delle wiki originali (es. non "Personaggi/Mondi/Oggetti" e basta, ma qualcosa di più tuo: "Il multiverso", "I Keyblade Wielders", "Dietro le quinte", "Le console del franchise"...).
- HTML semantico (`header`, `nav`, `main`, `section`, `article`, `footer`), niente `div` a caso.
- CSS scritto a mano: layout, tipografia, palette — niente framework ancora.

**Sei pronto per:** Selfwork HTML - Tag Generici, Selfwork HTML - Tag Semantici, Selfwork CSS base, Selfwork CSS avanzato 1 e 2.

---

### Milestone 2 — Bootstrap: il restyle
*(Modulo 3)*

**Obiettivo:** stessa pagina, ma responsive e con un grid system vero.

**Cosa fai:**
- Ricostruisci la homepage con Bootstrap: navbar, grid system per le categorie, card per ogni voce.
- Aggiungi una pagina "categoria" con un elenco di card (dati ancora finti).
- Verifica che sia leggibile da mobile a desktop senza scorciatoie hackerate.

**Sei pronto per:** Selfwork Bootstrap Grid System, Selfwork Bootstrap Components, Selfwork Live Coding Bootstrap.

---

### Milestone 3 — Git & GitHub: la storia comincia a contare
*(Modulo 4)*

**Obiettivo:** smettere di lavorare su un solo commit gigante.

**Cosa fai:**
- Riparti da qui con una storia pulita: un branch per ogni milestone completata finora (retroattivamente, se serve), poi da qui in avanti un branch per ogni nuova milestone.
- `.gitignore` sensato, commit piccoli con messaggi che spiegano il *perché* non il *cosa*.
- Push su GitHub (questo stesso repo).

**Sei pronto per:** Selfwork Git.

---

### Milestone 4 — JavaScript, parte 1: il linguaggio di base
*(Modulo 6, prima metà — fino a "Feedback Modulo Javascript Linguaggio 1")*

**Obiettivo:** cominciare a ragionare in dati e logica, non solo in markup.

**Cosa fai:**
- Sposta i dati finti delle categorie in variabili/array JS (ancora hardcoded: l'obiettivo qui è la logica, non l'API).
- Scrivi funzioni di utilità con condizioni e cicli: es. una funzione che filtra le voci per categoria, una che conta quante ce ne sono, una che segnala un errore di formato nei dati finti.

**Sei pronto per:** Selfwork Variabili, Operatori 1-4, Condizioni 1-2, Cicli 1-3.

---

### Milestone 5 — JavaScript, parte 2: strutture dati e funzioni
*(Modulo 6, seconda metà)*

**Obiettivo:** modellare i dati come li modellerà davvero un'API più avanti.

**Cosa fai:**
- Trasforma l'array piatto di prima in oggetti annidati che assomigliano alla forma di una risposta JSON reale (titolo, estratto, immagine, categoria, link alla fonte — anche se ancora finti, dagli già la forma che avranno i dati veri).
- Scrivi funzioni che lavorano su questi oggetti con i metodi degli array (`map`, `filter`, `find`...).

**Sei pronto per:** Selfwork Dati Strutturali, Funzioni 1-4, Array 1-2, Oggetti 1-3.

---

### Milestone 6 — JavaScript DOM: la prima chiamata vera
*(Modulo 7)*

**Obiettivo:** dati reali per la prima volta, e capire perché non basta il browser.

**Cosa fai:**
- Sostituisci i dati finti delle info "di contorno" (sviluppatori, Square Enix, storia della saga) con una vera chiamata `fetch` all'API di **Wikipedia** direttamente dal browser (ricorda `&origin=*`).
- Prova a fare lo stesso con l'API della **KH Wiki**: fallirà per CORS. Documenta l'errore in `/docs/note-fonti.md` e spiega a parole tue perché succede — è il ponte naturale verso il modulo PHP.
- Rendi la pagina interattiva: rendering dinamico delle card via DOM, ricerca/filtro lato client sui dati che hai.

**Sei pronto per:** Selfwork DOM 1-4 (compreso il DOM 4 sulle chiamate asincrone), Selfwork Presto.it (Frontend) — è letteralmente lo stesso tipo di esercizio, lo avrai già fatto qui.

---

### Milestone 7 — PHP: il primo backend, il proxy alla KH Wiki
*(Modulo 8)*

**Obiettivo:** risolvere il problema del CORS con un backend vero, e introdurre l'OOP.

**Cosa fai:**
- Script PHP (senza framework) che fa da proxy: riceve una richiesta dal tuo frontend, chiama l'API della KH Wiki server-side, normalizza la risposta in un JSON tuo, pulito.
- Passa alla OOP: una classe `WikiClient` (fa le chiamate HTTP), una `Category`/`Page` (rappresentano i dati), dependency injection tra loro invece di tutto scritto in un file unico.

**Sei pronto per:** Selfwork PHP 1-7, Selfwork PHP OOP 1-7.

---

### Milestone 8 — Laravel: il proxy diventa un'app
*(Modulo 9)*

**Obiettivo:** lo stesso proxy, ma strutturato come si deve.

**Cosa fai:**
- Rotte per `/personaggi`, `/mondi`, `/giochi`, `/sviluppatori`; controller che parlano con un service `WikiClient` (porta qui la classe del modulo precedente).
- Viste Blade con componenti riutilizzabili per le card (niente HTML duplicato tra pagine).
- Una form "segnala un errore in questa scheda" con invio mail vero.

**Sei pronto per:** Selfwork Rotte e Viste, Selfwork Rotte Parametriche, Selfwork Controller, Selfwork Components, Selfwork Invio Mail.

---

### Milestone 9 — Laravel + Database: smetti di chiamare l'API ad ogni click
*(Modulo 10, prima metà)*

**Obiettivo:** cache vera, autenticazione, CRUD.

**Cosa fai:**
- Migrazioni e modelli per salvare in cache le schede recuperate dalle API (rispetta il principio 3: niente chiamata live ad ogni visita).
- Un comando/job che sincronizza periodicamente i dati (il tuo "generatore di enciclopedia", il cuore del progetto).
- Autenticazione (Fortify) per un'area utente minimale: "la tua collezione personale" di preferiti.
- CRUD manuale sui preferiti prima di automatizzare oltre.

**Sei pronto per:** Selfwork Introduzione Database, Selfwork Modelli e Migrazioni, Selfwork File Storage e Validation Rules, Selfwork Fortify e Middleware, Selfwork CRUD.

---

### Milestone 10 — Relazioni e Livewire: il grafo del multiverso
*(Modulo 10, seconda metà)*

**Obiettivo:** collegare i dati tra loro, non solo elencarli.

**Cosa fai:**
- Relazione 1-N: un gioco ha molti personaggi/mondi collegati.
- Relazione N-N: un personaggio può comparire in più mondi/giochi, e viceversa (qui puoi finalmente collegare anche i dati delle wiki crossover, se ci arrivi).
- Livewire per ricerca e filtri reattivi senza reload di pagina, e per il CRUD dei preferiti fatto prima a mano.

**Sei pronto per:** Selfwork Relazione 1-N, Selfwork N-N, Selfwork Livewire, Selfwork CRUD Livewire.

---

### Milestone 11 — "Chiedi al Moogle": un assistente sui tuoi dati
*(Modulo 11 · Intelligenza Artificiale)*

**Obiettivo:** un piccolo RAG, non un chatbot generico.

**Cosa fai:**
- Prompt design su un caso semplice: rispondere a domande sul lore usando **solo** i dati che hai già in database (non quello che il modello sa già di suo — altrimenti non hai costruito niente).
- Un mini progetto RAG: gli estratti che hai salvato in cache diventano il contesto delle risposte.

**Sei pronto per:** Prompt Design e Utilizzo di OpenAI con Laravel, Progetto RAG con OpenAI (parte 1 e 2) — qui li affronterai avendo già un caso d'uso concreto invece che astratto.

---

### Metodologia: portalo avanti come un vero backlog
*(Modulo 12 · Metodologia Agile — trasversale, non è una milestone a sé)*

L'Agile non si impara in una milestone isolata, si vive lavorando: tieni fin dalla Milestone 0 un backlog personale (una colonna To do / Doing / Done, anche solo su carta o Trello), un'epica per milestone, una user story per ogni pezzo che costruisci ("Come visitatore, voglio filtrare i personaggi per mondo, per..."). Quando arrivi al Modulo 12 e studi Scrum, guarda indietro a come hai lavorato finora: è la prova migliore che hai capito perché queste pratiche esistono.

---

### Milestone 12 — Rifinitura e lancio
*(Modulo 13 · Progetto Finale)*

**Obiettivo:** un progetto finito, non un progetto abbandonato al 90%.

**Cosa fai:**
- Scrivi le user story mancanti per quello che hai rimandato finora, e chiudile.
- Deploy pubblico (una piattaforma gratuita va benissimo).
- Riscrivi questo stesso README come demo/presentazione del progetto finito: cosa fa, come è fatto, cosa rifaresti diversamente.

---

## Nota per te

Le prime milestone useranno dati finti apposta: non è un compromesso di cui vergognarsi, è perché non hai ancora gli strumenti per farlo "vero" (fetch e backend arrivano dopo). La promessa del progetto — un'enciclopedia generata dalle API, non scritta a mano — si realizza pienamente solo a partire dal Modulo 7 (PHP) in poi. Fino a lì, costruisci la casa; da lì in poi, ci fai entrare i dati veri.
