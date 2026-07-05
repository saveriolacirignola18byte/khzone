# KHZone

> La cronaca (non ufficiale) del multiverso di Kingdom Hearts — un'enciclopedia generata, non scritta a mano.

## L'idea

KHZone non è un sito su Kingdom Hearts scritto a mano: è un **client** che va a prendere le informazioni da wiki e fonti già esistenti tramite le loro API pubbliche, e le riorganizza in un prodotto più curato, coerente e navigabile di quanto lo siano gli originali.

Niente articoli scritti da zero, niente database di lore copiato a mano: ogni scheda (personaggio, mondo, keyblade, gioco, sviluppatore...) viene **generata** a partire da una richiesta API, con una tua libertà creativa solo su come organizzarla, presentarla e collegarla al resto. Se domani la wiki sorgente aggiorna una pagina, KHZone deve poterla recuperare di nuovo senza che tu debba toccare una riga di contenuto.

Il bello è che è un progetto reale, con tutti i problemi reali: dati che arrivano "sporchi", limiti di frequenza delle chiamate, CORS, licenze da rispettare, cache da progettare. Non un esercizio da corso, un prodotto vero — solo più piccolo.

Nasce apposta per allenarti meglio durante il corso, sui selfwork e in vista dell'esame finale — non per sostituirlo.

## Principi guida (non negoziabili)

1. **Niente hardcoding di contenuti "di lore".** Se è un'informazione su Kingdom Hearts (nome, descrizione, immagine, relazioni...), deve arrivare da un'API. I dati finti/placeholder vanno bene solo nelle primissime milestone, prima di avere gli strumenti per fare le chiamate vere — e lo dico esplicitamente milestone per milestone.
2. **Cita sempre la fonte.** I contenuti delle wiki Fandom sono licenziati CC BY-SA: ogni scheda deve linkare all'articolo originale. Non è solo educazione, è la licenza.
3. **Non martellare le API.** User-Agent descrittivo, cache lato server, niente chiamata live ad ogni singola visita di pagina una volta che hai un database a disposizione.
4. **Ogni milestone deve produrre qualcosa che gira**, anche se piccolo e brutto. Niente "lo rifinisco dopo" — quello dopo diventa il debito tecnico della milestone successiva.
5. **Un branch per milestone**, commit piccoli e frequenti con messaggi che spiegano il perché, non il cosa. *(Il progetto guidato del corso userà quasi certamente un flusso più semplice, commit diretti su `main` dopo ogni user story: va benissimo lì, è pensato per un team che deve andare veloce insieme. Qui invece ti alleni con branch e commit curati perché è l'abitudine che ti servirà davvero in azienda.)*
6. **Capisci, non memorizzare.** Non imparare a memoria i tag HTML, le proprietà CSS, i metodi degli array o i comandi Artisan: impara a riconoscere di che *tipo* di pezzo hai bisogno in un dato momento (un contenitore semantico, un modo per iterare, un modo per validare un input...) e come questi pezzi si combinano tra loro a livello logico. Il nome esatto e la sintassi precisa si cercano al bisogno — documentazione ufficiale, MDN, Stack Overflow — e restano in testa da soli, naturalmente, dopo che li hai scritti e cercati abbastanza volte. Impararli a memoria come prima cosa è tempo speso male: nel lavoro vero si cerca, non si recita. Vale per ogni tecnologia di questa roadmap, non solo per l'HTML.

## Le fonti dati

| Fonte | Endpoint | Cosa ci prendi | Note |
|---|---|---|---|
| **Kingdom Hearts Wiki** (Fandom) | `https://kingdomhearts.fandom.com/api.php` | Personaggi, mondi, keyblade, nemici, organizzazioni, terminologia | API MediaWiki standard (`action=query`, `action=parse`...). **Non ha CORS**: da browser diretto non funziona, serve un proxy server-side → arriva col modulo PHP. |
| **Wikipedia** | `https://it.wikipedia.org/w/api.php` (o `en.`) | Square Enix, Tetsuya Nomura, storia della saga, piattaforme/console, sviluppatori | Supporta CORS con il parametro `&origin=*` → richiamabile anche direttamente da JS lato client. Buon primo esperimento col modulo DOM/Fetch. |
| **RAWG** *(facoltativo, milestone avanzate)* | `https://api.rawg.io/api/` | Catalogo giochi per piattaforma, copertine, date di uscita | Richiede una API key gratuita → occasione per imparare a gestire secrets con `.env` in Laravel. In alternativa, per chi vuole strafare: **IGDB** (più ricco, ma richiede OAuth via Twitch). |
| **Altre wiki Fandom** *(stretch finale)* | es. `https://finalfantasy.fandom.com/api.php` | Sezione "cameo e crossover" (KH pesca da FF, Disney, ecc.) | Stessa identica API della KH Wiki, cambia solo il sottodominio: quasi a costo zero una volta che il client è generico. |

Prima regola pratica: non fidarti di questa tabella e basta. Ogni wiki MediaWiki espone una sandbox interattiva su `/wiki/Special:ApiSandbox` — usala per esplorare da solo cosa puoi chiedere prima di scrivere codice. Con `action=query&meta=siteinfo&siprop=extensions` scopri anche quali estensioni (es. estrazione di testo semplice) sono attive su quella specifica wiki: non tutte le wiki Fandom hanno le stesse.

Per esplorare e testare queste chiamate senza scrivere ancora codice (e più avanti per testare le tue stesse rotte Laravel), usa **Bruno**: è un amico, un client di API testing open-source alternativo a Postman. La differenza che conta per te: Bruno salva le collection come file di testo semplice (`.bru`) invece che in un formato proprietario, quindi puoi versionarle in git insieme al codice — una cartella tipo `/api-tests` con le richieste già pronte è documentazione viva del progetto, non uno strumento usa-e-getta.

Esempio verificato e funzionante (KH Wiki, elenco pagine di una categoria):
```
https://kingdomhearts.fandom.com/api.php?action=query&list=categorymembers&cmtitle=Category:Keyblades&format=json&cmlimit=20
```

## Come è organizzata la roadmap

Ogni milestone corrisponde (più o meno) a un modulo del corso strutturato. La regola d'oro: **costruisci solo con gli strumenti che hai già visto fino a quel punto**. Niente framework prima del tempo, niente scorciatoie prese in prestito da moduli futuri — se in una milestone ti manca uno strumento per fare le cose "come si deve", è voluto: userai dati finti o soluzioni più rozze, e tornerai a sistemarle quando avrai lo strumento giusto. È così che funziona anche nel lavoro vero.

Alla fine di ogni milestone trovi una checklist **"Sei pronto per"**: se sai rispondere di sì a ogni punto, vai ad affrontare i selfwork ufficiali di quel modulo con fiducia — li avrai già praticati, applicati a un problema vero invece che a un esercizio isolato.

Ogni milestone elenca anche cosa **"Da approfondire"**: sono nomi e parole chiave, non una lista da imparare a memoria — sono lo spunto per sapere cosa cercare quando ti serve (principio 6). Se durante una milestone ti accorgi di stare a ripetere a mente la sintassi di qualcosa invece di capire quando/perché ti serve, è un segnale che stai studiando nel modo sbagliato.

## Nota per te

Le prime milestone useranno dati finti apposta: non è un compromesso di cui vergognarsi, è perché non hai ancora gli strumenti per farlo "vero" (fetch e backend arrivano dopo). La promessa del progetto — un'enciclopedia generata dalle API, non scritta a mano — si realizza pienamente solo a partire dal Modulo 7 (PHP) in poi. Fino a lì, costruisci la casa; da lì in poi, ci fai entrare i dati veri.

---

### Milestone 0 — Setup, Git e branch workflow, metodo di studio
*(Modulo 1 · Introduzione e Fondamenti — Modulo 4 · Git & GitHub, vista in anticipo — Modulo 5 · Apprendimento e Problem Solving)*

**Obiettivo:** repo pronto, workflow git corretto fin dal primo commit, ambiente e metodo di studio rodati. Questa repo probabilmente finirà nel tuo portfolio: vale la pena non prendere brutte abitudini proprio all'inizio.

**Cosa fai:**
- Prima ancora di scrivere una riga di HTML, guarda la lezione "Git & GitHub" del Modulo 4 — puoi saltarla avanti nel corso, non dipende da nient'altro. Configura il repo: `.gitignore` sensato, un branch dedicato per ogni milestone (mai commit diretti su `main`), commit piccoli con messaggi che spiegano il perché non il cosa.
- Struttura iniziale del repo (`index.html` vuoto, cartella `/assets`, cartella `/docs`) sul primo branch di lavoro, non su `main`.
- In `/docs/note-fonti.md`, esplora a mano (da browser, senza scrivere codice) 3-4 query diverse sull'API della KH Wiki e annota cosa restituiscono e cosa non ti aspettavi.
- Apri la `Special:ApiSandbox` della KH Wiki e prova almeno un paio di azioni diverse da quella mostrata sopra.
- Carica le lezioni dei Moduli 1 e 5 su NotebookLM e scriviti un tuo mini metodo di studio: come organizzi le sessioni, e in che ordine cerchi aiuto quando resti bloccato. Un ordine che funziona: **NotebookLM** per la teoria del corso, **ricerca su Google/Stack Overflow** "alla vecchia maniera" per problemi pratici già affrontati da altri (è ancora il modo più veloce per capire un errore preciso) — e solo se il dubbio è più di fondo, concettuale o architetturale, chiedi a **Claude**. **ChatGPT resta un fallback**, da usare solo se Claude non è disponibile: mai la prima scelta, e mai per farti risolvere un esercizio al posto tuo.

**Da approfondire:** `git branch`, `git checkout -b`/`git switch -c`, `git merge` (fast-forward vs no-ff), `git log --oneline --graph`, differenza tra branch locale e remoto, `git push -u origin <branch>`; sintassi Markdown di base; MediaWiki Action API (`action=query`, `list=categorymembers`, `action=parse`, `meta=siteinfo&siprop=extensions`); perché le API pubbliche hanno rate limit e cosa succede se li ignori.

**Sei pronto per:** Console Unix, Visual Studio Code, i quiz del Modulo 1, Selfwork Git.

---

### Milestone 1 — HTML & CSS: la prima pagina statica
*(Modulo 2)*

**Obiettivo:** struttura semantica e stile, zero logica, zero dati veri.

**Cosa fai:**
- Homepage statica con dati **finti e scritti a mano** (è l'unica milestone dove è esplicitamente ok): 4-5 categorie tematiche a scelta tua, diverse dalla struttura standard delle wiki originali (es. non "Personaggi/Mondi/Oggetti" e basta, ma qualcosa di più tuo: "Il multiverso", "I Keyblade Wielders", "Dietro le quinte", "Le console del franchise"...).
- HTML semantico (`header`, `nav`, `main`, `section`, `article`, `footer`), niente `div` a caso.
- CSS scritto a mano: layout, tipografia, palette — niente framework ancora.

Qui vale più che altrove il principio 6: non serve sapere a memoria ogni tag o ogni proprietà CSS esistente. Serve capire quali *tipi* di blocco esistono (contenitore generico vs semantico, testo, lista, immagine...) e come si compongono; il nome preciso e la sintassi li cerchi mentre scrivi, e restano naturalmente dopo qualche ripetizione.

**Da approfondire:** elementi semantici HTML5 (`header`, `nav`, `main`, `section`, `article`, `aside`, `footer`) e perché non sono intercambiabili con `div`; box model CSS (content/padding/border/margin); selettori CSS e specificità; Flexbox (`display:flex`, `justify-content`, `align-items`, `flex-wrap`) per allineare le card senza framework; unità relative (`rem`, `em`, `%`) vs assolute (`px`); meta tag di base (`viewport`, `charset`) e favicon.

**Sei pronto per:** Selfwork HTML - Tag Generici, Selfwork HTML - Tag Semantici, Selfwork CSS base, Selfwork CSS avanzato 1 e 2.

---

### Milestone 2 — Bootstrap: il restyle
*(Modulo 3)*

**Obiettivo:** stessa pagina, ma responsive e con un grid system vero.

**Cosa fai:**
- Ricostruisci la homepage con Bootstrap: navbar, grid system per le categorie, card per ogni voce.
- Aggiungi una pagina "categoria" con un elenco di card (dati ancora finti).
- Verifica che sia leggibile da mobile a desktop senza scorciatoie hackerate.

**Da approfondire:** grid system Bootstrap (container/row/col, breakpoint `sm md lg xl xxl`); utility class vs classi dei componenti; CDN vs installazione via npm (e perché questa scelta si ripercuote quando arriverà un bundler); componenti navbar, card, badge, modal.

**Sei pronto per:** Selfwork Bootstrap Grid System, Selfwork Bootstrap Components, Selfwork Live Coding Bootstrap.

---

### Milestone 3 — JavaScript, parte 1: il linguaggio di base
*(Modulo 6, prima metà — fino a "Feedback Modulo Javascript Linguaggio 1")*

**Obiettivo:** cominciare a ragionare in dati e logica, non solo in markup.

**Cosa fai:**
- Sposta i dati finti delle categorie in variabili/array JS (ancora hardcoded: l'obiettivo qui è la logica, non l'API).
- Scrivi funzioni di utilità con condizioni e cicli: es. una funzione che filtra le voci per categoria, una che conta quante ce ne sono, una che segnala un errore di formato nei dati finti.

**Da approfondire:** `var` vs `let` vs `const` e scope (block scope vs function scope); tipi primitivi e coercizione implicita, `==` vs `===`; operatori logici e short-circuit (`&&`, `||`, `??`); cicli `for`, `while`, `for...of`; template literals.

**Sei pronto per:** Selfwork Variabili, Operatori 1-4, Condizioni 1-2, Cicli 1-3.

---

### Milestone 4 — JavaScript, parte 2: strutture dati e funzioni
*(Modulo 6, seconda metà)*

**Obiettivo:** modellare i dati come li modellerà davvero un'API più avanti.

**Cosa fai:**
- Trasforma l'array piatto di prima in oggetti annidati che assomigliano alla forma di una risposta JSON reale (titolo, estratto, immagine, categoria, link alla fonte — anche se ancora finti, dagli già la forma che avranno i dati veri).
- Scrivi funzioni che lavorano su questi oggetti con i metodi degli array (`map`, `filter`, `find`...).

**Da approfondire:** metodi array `map`/`filter`/`reduce`/`find`/`forEach` e quali mutano l'array originale e quali ne creano uno nuovo; destructuring di array/oggetti; spread/rest operator; `Object.keys`/`Object.values`/`Object.entries`; `JSON.stringify`/`JSON.parse` (li userai per davvero appena arrivano i dati reali dalle API).

**Sei pronto per:** Selfwork Dati Strutturali, Funzioni 1-4, Array 1-2, Oggetti 1-3.

---

### Milestone 5 — JavaScript DOM: la prima chiamata vera
*(Modulo 7)*

**Obiettivo:** dati reali per la prima volta, e capire perché non basta il browser.

**Cosa fai:**
- Sostituisci i dati finti delle info "di contorno" (sviluppatori, Square Enix, storia della saga) con una vera chiamata `fetch` all'API di **Wikipedia** direttamente dal browser (ricorda `&origin=*`).
- Prova a fare lo stesso con l'API della **KH Wiki**: fallirà per CORS. Documenta l'errore in `/docs/note-fonti.md` e spiega a parole tue perché succede — è il ponte naturale verso il modulo PHP.
- Rendi la pagina interattiva: rendering dinamico delle card via DOM, ricerca/filtro lato client sui dati che hai.

**Da approfondire:** `document.querySelector`/`querySelectorAll`, `addEventListener`; `createElement`/`appendChild` vs `innerHTML` (e perché mettere dati esterni in `innerHTML` senza attenzione è un rischio — concetto di XSS); `fetch`, `Promise`, `async`/`await`, `try/catch` per gli errori di rete; **CORS** e Same-Origin Policy — non solo "l'errore che ho preso", ma perché il browser lo blocca a prescindere da cosa fai tu.

**Sei pronto per:** Selfwork DOM 1-4 (compreso il DOM 4 sulle chiamate asincrone), Selfwork Presto.it (Frontend) — è letteralmente lo stesso tipo di esercizio, lo avrai già fatto qui.

---

### Milestone 6 — PHP: il primo backend, il proxy alla KH Wiki
*(Modulo 8)*

**Obiettivo:** risolvere il problema del CORS con un backend vero, e introdurre l'OOP.

**Cosa fai:**
- Prima di scrivere il proxy, esplora e salva in Bruno le chiamate che ti servono davvero verso la KH Wiki (parametri, risposta, errori) — è più veloce che scoprirlo a suon di `var_dump` nel codice.
- Script PHP (senza framework) che fa da proxy: riceve una richiesta dal tuo frontend, chiama l'API della KH Wiki server-side, normalizza la risposta in un JSON tuo, pulito.
- Passa alla OOP: una classe `WikiClient` (fa le chiamate HTTP), una `Category`/`Page` (rappresentano i dati), dependency injection tra loro invece di tutto scritto in un file unico.

**Da approfondire:** superglobal (`$_GET`, `$_POST`, `$_SERVER`); `file_get_contents`/cURL per chiamate HTTP in uscita, gestione di status code e timeout; `json_decode`/`json_encode`; classi, costruttori, visibilità (`public`/`private`/`protected`); namespace e autoloading PSR-4 via Composer; **Dependency Injection** — perché passare un `WikiClient` già pronto al costruttore è meglio che istanziarlo dentro la classe che lo usa.

**Sei pronto per:** Selfwork PHP 1-7, Selfwork PHP OOP 1-7.

---

### Milestone 7 — Laravel: il proxy diventa un'app
*(Modulo 9)*

**Obiettivo:** lo stesso proxy, ma strutturato come si deve.

**Cosa fai:**
- Rotte per `/personaggi`, `/mondi`, `/giochi`, `/sviluppatori`; controller che parlano con un service `WikiClient` (porta qui la classe del modulo precedente).
- Viste Blade con componenti riutilizzabili per le card (niente HTML duplicato tra pagine).
- Una form "segnala un errore in questa scheda" con invio mail vero.
- Una collection Bruno con le richieste alle tue nuove rotte (`/personaggi`, `/mondi`...), versionata in `/api-tests` insieme al codice: da qui in poi, quando cambi una rotta, aggiorni anche la richiesta di test.

**Da approfondire:** `php artisan make:controller`/`make:mail`; routing (`Route::get`, route model binding, named routes); direttive Blade (`@if`, `@foreach`, `@component`/`<x-component>`) e differenza tra `{{ }}` (escaping automatico) e `{!! !!}`; facade `Illuminate\Support\Facades\Http` per le chiamate esterne lato server; `.env` e `config/services.php` per gestire secrets/API key senza committarle; Mailable e un driver di test per le email (es. log o Mailtrap).

**Sei pronto per:** Selfwork Rotte e Viste, Selfwork Rotte Parametriche, Selfwork Controller, Selfwork Components, Selfwork Invio Mail.

---

### Milestone 8 — Laravel + Database: smetti di chiamare l'API ad ogni click
*(Modulo 10, prima metà)*

**Obiettivo:** cache vera, autenticazione, CRUD.

**Cosa fai:**
- Migrazioni e modelli per salvare in cache le schede recuperate dalle API (rispetta il principio 3: niente chiamata live ad ogni visita).
- Un comando/job che sincronizza periodicamente i dati (il tuo "generatore di enciclopedia", il cuore del progetto).
- File storage vero: le immagini delle schede (prese dalla wiki via `prop=images`/`imageinfo`) non vanno linkate a caldo dalla fonte esterna, ma scaricate e salvate nel tuo storage Laravel, con validazione (dimensione, mimetype) prima di salvarle.
- Autenticazione (Fortify) per un'area utente minimale: "la tua collezione personale" di preferiti.
- CRUD manuale sui preferiti prima di automatizzare oltre.

**Da approfondire:** `php artisan make:model -m` e lo Schema builder nelle migration; Eloquent di base (query builder, `$fillable`/`$guarded`); Task Scheduling (`Kernel::schedule`, `php artisan schedule:run`) per il job di sync periodico; Jobs e Queue — perché non vuoi bloccare una richiesta HTTP mentre sincronizzi centinaia di pagine dalla wiki; cosa fa "sotto" Laravel Fortify (le route di login/registrazione che ti genera) e il middleware `auth`; facade `Storage` (`Storage::disk`, `putFile`); validazione (`$request->validate()`, Form Request).

**Sei pronto per:** Selfwork Introduzione Database, Selfwork Modelli e Migrazioni, Selfwork File Storage e Validation Rules, Selfwork Fortify e Middleware, Selfwork CRUD.

---

### Milestone 9 — Relazioni e Livewire: il grafo del multiverso
*(Modulo 10, seconda metà)*

**Obiettivo:** collegare i dati tra loro, non solo elencarli.

**Cosa fai:**
- Relazione 1-N: un gioco ha molti personaggi/mondi collegati.
- Relazione N-N: un personaggio può comparire in più mondi/giochi, e viceversa (qui puoi finalmente collegare anche i dati delle wiki crossover, se ci arrivi).
- Livewire per ricerca e filtri reattivi senza reload di pagina, e per il CRUD dei preferiti fatto prima a mano.
- Ricerca full-text sulle schede in cache (Laravel Scout + un driver semplice tipo TNTSearch): è la stessa esigenza che troverai nel progetto guidato del corso, qui la risolvi su dati che hai generato tu.
- *(Bonus, facoltativo)* un ruolo "curatore": un utente autenticato con un permesso in più che può segnalare una scheda come "da risincronizzare" prima del prossimo giro del job — piccola prova del concetto di ruoli/permessi che ritroverai nel progetto guidato (lì con ruoli come "revisore" o "writer").

**Da approfondire:** relazioni Eloquent `hasMany`, `belongsTo`, `belongsToMany` e tabelle pivot; eager loading (`with()`) e il problema delle N+1 query — non solo come si scrive, ma perché serve; ciclo di vita di un componente Livewire (`mount`, `render`, `wire:model`, `wire:click`); driver di Laravel Scout (TNTSearch per iniziare; Meilisearch/Algolia se vuoi approfondire); concetto di ruoli/permessi, anche solo un booleano `is_curatore`, o il pacchetto `spatie/laravel-permission` se vuoi vedere come si fa "sul serio".

**Sei pronto per:** Selfwork Relazione 1-N, Selfwork N-N, Selfwork Livewire, Selfwork CRUD Livewire.

---

### Milestone 10 — "Chiedi al Moogle": un assistente sui tuoi dati
*(Modulo 11 · Intelligenza Artificiale)*

**Obiettivo:** un piccolo RAG, non un chatbot generico.

**Cosa fai:**
- Prompt design su un caso semplice: rispondere a domande sul lore usando **solo** i dati che hai già in database (non quello che il modello sa già di suo — altrimenti non hai costruito niente).
- Un mini progetto RAG: gli estratti che hai salvato in cache diventano il contesto delle risposte.

**Da approfondire:** differenza tra "il modello lo sa già" e "il modello legge quello che gli metti nel prompt" (grounding); endpoint Chat Completions vs Embeddings di OpenAI; concetto di embedding e similarità vettoriale (a livello teorico, non serve implementarlo da zero); prompt engineering di base — system prompt vs user prompt, few-shot examples; il pattern RAG (Retrieval-Augmented Generation) a grandi linee.

**Sei pronto per:** Prompt Design e Utilizzo di OpenAI con Laravel, Progetto RAG con OpenAI (parte 1 e 2) — qui li affronterai avendo già un caso d'uso concreto invece che astratto.

---

### Metodologia: portalo avanti come un vero backlog
*(Modulo 12 · Metodologia Agile — trasversale, non è una milestone a sé)*

L'Agile non si impara in una milestone isolata, si vive lavorando: tieni fin dalla Milestone 0 un backlog personale (una colonna To do / Doing / Done, anche solo su carta o Trello), un'epica per milestone, una user story per ogni pezzo che costruisci ("Come visitatore, voglio filtrare i personaggi per mondo, per..."). Quando arrivi al Modulo 12 e studi Scrum, guarda indietro a come hai lavorato finora: è la prova migliore che hai capito perché queste pratiche esistono.

Un allenamento in più, utile per il vero esame: ogni 2-3 milestone, fatti una mini "demo" a voce alta di 5 minuti (anche da solo, o a un amico) di quello che hai costruito — cosa funziona, cosa no, cosa faresti diversamente. Il progetto finale del corso si valuta così: una demo intermedia da **massimo 10 minuti** e una demo finale da **massimo 15 minuti** con tutto il team come relatore. Arrivarci avendo già preso confidenza nel presentare il tuo lavoro a voce, invece che solo per iscritto, è un vantaggio vero.

---

### Milestone 11 — Rifinitura e lancio
*(Modulo 13 · Progetto Finale)*

**Obiettivo:** un progetto finito, non un progetto abbandonato al 90%.

**Cosa fai:**
- Scrivi le user story mancanti per quello che hai rimandato finora, e chiudile.
- Deploy pubblico (una piattaforma gratuita va benissimo, anche la più semplice — se poi ti avanza tempo, le Milestone 12-13 qui sotto ti portano a un deploy vero con CI/CD).

**Da approfondire:** checklist di "definition of done" per una user story (non solo "il codice gira", anche: gestione errori, dati mancanti dalla wiki, mobile); differenza tra hosting statico e hosting di un'app con backend+database.

Se sei arrivato fin qui seguendo tutte le milestone, il corso è finito ma KHZone no: quelle qui sotto sono extra, da fare solo se hai tempo prima dell'esame — non sono legate a nessun modulo specifico, ma a cose che in azienda (EY compresa) userai comunque.

---

## Extra — se ti avanza tempo prima dell'esame

Queste tre non sono legate a un modulo del corso: sono pratiche DevOps che userai davvero appena entri in un'azienda, e avercele già in un progetto tuo (portfolio) vale più di mille righe di CV.

### Milestone 12 — Immagine Docker via GitHub Actions

**Obiettivo:** ogni push su `main` costruisce da solo un'immagine Docker dell'app, senza che tu debba farlo a mano.

Verificato: fattibile completamente gratis. Su repository pubblica (la tua lo è), i runner standard di GitHub Actions non hanno limite di minuti, e il **GitHub Container Registry** (`ghcr.io`) non ha limiti di storage/banda per le immagini pubbliche.

**Cosa fai:**
- Scrivi un `Dockerfile` per KHZone (immagine PHP-FPM/Apache con le estensioni che Laravel richiede, o multi-stage se vuoi tenerla leggera: uno stage con Composer/npm per buildare gli asset, uno stage finale solo runtime).
- `.dockerignore` (niente `vendor/`, `node_modules/`, `.env` nell'immagine).
- Workflow GitHub Actions (`.github/workflows/docker.yml`) che al push su `main`: builda l'immagine, fa login su `ghcr.io` con il `GITHUB_TOKEN` automatico (non serve un secret tuo), la pusha con un tag (es. lo SHA del commit, più `latest`).

**Da approfondire:** istruzioni Dockerfile (`FROM`, `COPY`, `RUN`, `CMD`/`ENTRYPOINT`) e multi-stage build; `.dockerignore`; sintassi di un workflow GitHub Actions (`on: push`, `jobs`, `steps`) e le action ufficiali `actions/checkout`, `docker/login-action`, `docker/build-push-action`; differenza tra un tag `latest` e un tag legato al commit/versione.

---

### Milestone 13 — Deploy su Render

**Obiettivo:** l'immagine che Actions costruisce arriva online da sola.

Verificato: fattibile gratis, con due limiti reali da conoscere prima di partire (le condizioni free cambiano spesso: verificale di nuovo quando ci arrivi).

- Il piano free ha **750 ore/mese** e il servizio va in stand-by dopo ~15 minuti di inattività (primo avvio dopo il risveglio: circa 1 minuto).
- Il **filesystem è effimero**: qualsiasi cosa scrivi su disco (es. un file SQLite) sparisce ad ogni riavvio/redeploy. Il Postgres gratuito di Render invece è persistente, ma **scade 30 giorni dopo la creazione** (+ 14 giorni di grazia prima che venga cancellato del tutto).

Questo secondo limite in realtà è quasi un vantaggio per te: ti costringe a verificare sul serio che il tuo comando di sync (Milestone 8) rigeneri l'intera enciclopedia da zero, dato che ogni tanto dovrai ricreare il database e ripartire da un `php artisan khzone:sync` pulito. Se KHZone non sopravvive a un database vuoto, la promessa "tutto rigenerabile dalle API" non è ancora vera.

**Cosa fai:**
- Servizio Render configurato per fare il deploy dell'immagine da `ghcr.io` (non dal codice sorgente: dell'immagine che hai già costruito in Milestone 12).
- Variabili d'ambiente (incluse le chiavi delle API esterne) configurate sulla piattaforma, mai committate.
- Un **Deploy Hook** di Render richiamato come step finale del workflow GitHub Actions, così ogni push aggiorna anche l'ambiente pubblico in automatico.

**Da approfondire:** differenza tra "Deploy from Git" e "Deploy an existing image" su Render; Deploy Hook (URL da chiamare via `curl` a fine pipeline); gestione delle variabili d'ambiente in produzione; cosa significa "cold start" per un servizio free.

---

### Milestone 14 — Il README diventa un vero README

**Obiettivo:** questo file, che finora hai usato come roadmap per te stesso, diventa il README che un sconosciuto legge per capire cos'è KHZone.

**Cosa fai:**
- Riscrivi il contenuto (puoi tenere questo file come `ROADMAP.md` a parte, se vuoi conservare il percorso fatto): sezione "Cos'è", "Screenshot/demo", "Funzionalità", "Stack tecnico", "Come farlo girare in locale" (requisiti, `.env.example`, comandi), licenza.
- Aggiungi badge (anche solo markdown: stato della build, licenza) e uno screenshot o una GIF della homepage.
- Fai leggere il README a qualcuno che non conosce il progetto e guarda dove si blocca: quello è quello che manca.

**Da approfondire:** differenza tra un README "per me" (una roadmap) e un README "per chi arriva da fuori" (badge, screenshot, sezione Features, Tech stack, Getting Started); come si scrive una sezione "Getting Started" che funziona senza contesto pregresso.

---

### Milestone 15 — Il diagramma che spiega tutto

**Obiettivo:** un diagramma che chiunque — anche tu, tra sei mesi — possa guardare e capire come gira KHZone senza leggere una riga di codice.

**Cosa fai:**
- Con **draw.io** (diagrams.net — gratuito, esiste anche come estensione VS Code) disegna il flusso completo: richiesta del browser → rotta Laravel → controller → service `WikiClient` → API esterne (KH Wiki, Wikipedia, RAWG) → cache/database → job di sync asincrono → risposta (Blade/Livewire) → utente. Se hai fatto le Milestone 11-13, includi anche dove si inseriscono Docker/Render nel quadro.
- Salva il file sorgente `.drawio` (è XML, si versiona in git senza problemi) in `/docs/architettura.drawio`, ed esporta anche un `.svg`/`.png` da incorporare nel README con `![architettura](docs/architettura.png)`.
- *(Bonus, facoltativo)* un secondo diagramma più a grana fine solo sul flusso dati: per ogni campo di una scheda (nome, immagine, descrizione...), da quale API arriva, che trasformazione subisce, dove viene cachato.

**Da approfondire:** differenza tra un diagramma di flusso (decisioni/passi in sequenza) e uno architetturale (componenti e come comunicano tra loro); un'occhiata al C4 model (solo i primi due livelli, come ispirazione, non serve seguirlo alla lettera); perché conviene versionare il sorgente `.drawio` e non solo l'immagine esportata.
