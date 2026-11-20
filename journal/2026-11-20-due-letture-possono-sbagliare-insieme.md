# Due letture concordi possono sbagliare insieme

20 novembre 2026

Due motori OCR leggono lo stesso codice nello stesso modo. Il confronto non trova differenze. Il codice, però, è sbagliato: entrambi hanno confuso gli stessi caratteri. È uno dei casi che ci ha aiutato a precisare il passo successivo di PIGUARD.

Lavoriamo su un terreno probabilistico. Non cerchiamo un componente infallibile: cerchiamo una decisione migliore, costruita con evidenze verificabili e con un costo esplicito per gli errori e gli approfondimenti. Da oggi fissiamo questo criterio architetturale: **un solo modello per ogni punto decisionale probabilistico non ci basta**. È la direzione del progetto, non una descrizione di integrazioni già completate.

## Prima separare le letture

Abbiamo affiancato Tesseract e docTR, conservando entrambe le trascrizioni. Un secondo OCR può recuperare testo perso, ma può anche introdurre errori dove il primo aveva letto correttamente. Per questo non sostituiamo automaticamente una lettura con l'altra.

Il giudice successivo riceve un compito limitato: valutare l'impatto delle differenze e indicare se sia giustificato approfondire. Non deve correggere il testo, scegliere arbitrariamente la lettura vera o emettere un verdetto di sicurezza sul documento.

Abbiamo usato 26 ritagli di sviluppo: dodici famiglie testuali con una versione pulita e una incerta, più due controlli vuoti. Le etichette sono state congelate prima delle inferenze. Cinque casi presentavano omissioni, cambiamenti di numeri/codici o testo gravemente corrotto che richiedevano approfondimento secondo il criterio stabilito.

## Confrontare i giudici senza spostare il bersaglio

Laya Multilingual su CPU, poi Clef-Flash9B, Kev-9B e Nimble-9B su GPU hanno ricevuto gli stessi testi e le stesse domande. Per ciascun modello abbiamo ripetuto ogni caso scambiando le letture, invertendo l'ordine delle opzioni e applicando entrambe le variazioni: 104 richieste e 208 decisioni per modello.

Non abbiamo riaddestrato i modelli, modificato le etichette dopo le risposte o scelto a posteriori la presentazione che rendeva meglio. I formati interni e i tokenizer restano diversi; la copertura degli input è stata verificata separatamente nei runtime.

Risultati nella presentazione base:

| Metodo | Approfondimenti necessari richiesti, su 5 | Necessari incerti | Necessari omessi | Richieste superflue sugli altri 21 | Incerti sugli altri 21 |
|---|---:|---:|---:|---:|---:|
| Approfondire ogni disaccordo | 5 | 0 | 0 | 10 | 0 |
| Laya | 1 | 0 | 4 | 14 | 0 |
| Clef | 4 | 1 | 0 | 0 | 7 |
| Kev | 4 | 1 | 0 | 3 | 4 |
| Nimble | 2 | 0 | 3 | 5 | 0 |

Sono misure di **triage OCR su un piccolo campione di sviluppo**, non percentuali di rilevamento della prompt injection. "Superfluo" significa non giustificato dal criterio testuale congelato; non dimostra che l'immagine originale sia priva di errori.

Laya non è stato promosso. Clef e Kev meritano un seguito controllato; Nimble non ha mostrato un vantaggio sufficiente in questa prova. Non stiamo dichiarando un vincitore universale né scartando intere famiglie sulla base di 26 ritagli.

## L'incertezza ha un costo

Dire che Clef ha prodotto zero richieste superflue esplicite sarebbe incompleto senza indicare le sette astensioni sugli altri casi. Se ogni astensione richiede ulteriore lavoro, Clef e Kev comportano entrambi dodici approfondimenti nella prova base: cinque necessari e sette ulteriori, contro quindici della regola che approfondisce ogni disaccordo.

La presentazione conta. Scambiare le letture cambia la scelta di approfondire in quattro casi per Clef e Kev, cinque per Nimble. Invertire le opzioni la cambia rispettivamente in zero, cinque e sette casi. La stessa conclusione aggregata può nascondere una diversa ripartizione tra decisione e astensione: conserveremo entrambe.

Sull'A40, le mediane per richiesta con due domande sono state circa 0,166 secondi per Clef, 0,155 per Kev e 0,447 per Nimble. Escludono caricamento e download; non sono un confronto hardware equo con Laya su CPU. Questi tempi riguardano testi brevi già estratti, non la scansione completa di un PDF.

## L'accordo non crea informazione nuova

I tre accordi OCR sbagliati restano senza richiesta di approfondimento da parte di Clef, Kev e Nimble, in tutte le presentazioni. I giudici non vedevano l'immagine: ricevevano due stringhe identiche. La loro concordanza non restituisce l'informazione persa prima.

Questo vale anche tra modelli. Clef e Kev condividono la famiglia Qwen; l'accordo di due discendenti non può essere contato automaticamente come due prove indipendenti. Misureremo gli errori condivisi e il contributo aggiuntivo della coppia. Le probabilità dichiarate non vanno semplicemente mediate: calibrazione e pesi richiedono dati separati.

## Il prossimo passo

Prepariamo campioni indipendenti, con fonti e famiglie separate dai casi già usati. Congeleremo input, annotazioni e protocollo combinato prima di osservare le nuove risposte. Confronteremo Clef e Kev singolarmente e insieme, conservando disaccordi, astensioni ed errori tecnici come stati distinti.

Per gli accordi sbagliati servirà un esperimento distinto che aggiunga informazione visiva o una lettura indipendente. Le trascrizioni originali resteranno disponibili. Nessun nuovo giudice è stato attivato nella pipeline: il lavoro di oggi costruisce una base misurabile per decidere come combinarli.

Modelli esaminati: [Laya Multilingual](https://huggingface.co/convaiinnovations/laya-multilingual), [Clef-Flash](https://huggingface.co/Cloudflare/clef-flash), [Kev](https://github.com/jaredpalmer/kev), [Bespoke-Nimble-9B](https://huggingface.co/bespokelabs/Bespoke-Nimble-9B). I risultati riportati qui sono quelli del nostro esperimento, non i benchmark dei produttori.
