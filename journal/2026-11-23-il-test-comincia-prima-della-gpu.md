# Il test comincia prima della GPU

23 novembre 2026

Prima di chiedere a due modelli di giudicare gli stessi testi, bisogna verificare che ricevano davvero i testi previsti. Oggi abbiamo preparato il prossimo confronto Clef/Kev per PIGUARD, senza eseguire nuove inferenze dei giudici. È emerso un dettaglio che cambia come interpretare una delle nostre prove precedenti.

## Dal campione sintetico a due documenti esterni

Abbiamo scelto sei pagine da due PDF pubblici: il [modulo IRS W-9](https://www.irs.gov/pub/irs-pdf/fw9.pdf) e il [NIST AI Risk Management Framework](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-1.pdf). Per ogni pagina abbiamo preparato una versione pulita e una degradata, fissando selezione e trasformazione prima di eseguire Tesseract e docTR.

Le dodici coppie OCR sono state completate su CPU. Abbiamo conservato immagini, trascrizioni grezze e riferimenti, senza fondere le letture. Le annotazioni preliminari identificano otto casi da approfondire e quattro controlli con differenze non sostanziali secondo il criterio scelto. **Sono etichette di triage OCR, non risultati dei modelli né percentuali di rilevamento della prompt injection.**

Due fonti inglesi non rappresentano il dominio dei documenti. Le versioni pulita e degradata della stessa pagina sono correlate; l'annotazione ha un solo revisore. Questo è un pilota esterno ai precedenti casi sintetici, non una validazione generale.

## Gli accordi sbagliati esistono anche nei documenti ordinari

Dentro le nuove letture abbiamo individuato tre passaggi concordi ma inesatti: in due casi entrambi gli OCR leggono “retum” invece di “return”; in un riferimento numerato leggono “Il” invece di “II”.

Abbiamo isolato questi passaggi senza correggerli e affiancato tre accordi corretti, mantenendo posizione, ritaglio e collegamento alle letture originali. I sei frammenti costituiscono un pannello diagnostico separato. Sono stati selezionati dopo l'OCR e provengono dalle stesse pagine: non diventano sei nuovi documenti indipendenti.

Il giudice testuale riceverà le due letture, senza immagine. Una scelta di non approfondire non dimostrerà la fedeltà ai pixel. Terremo separati il giudizio sulle differenze osservabili e l'errore effettivamente presente nell'immagine: il secondo richiede una fonte d'informazione aggiuntiva.

## Quando invertire le opzioni non cambia niente

Per ogni unità abbiamo preparato quattro presentazioni: base, letture scambiate, opzioni invertite ed entrambe le variazioni. Il pilota contiene quindi 72 richieste per modello.

Abbiamo eseguito su CPU i veri encoder dei due runtime, senza caricare i pesi, confrontando le sequenze complete con quelle ammesse dai limiti del runner. Non ci sono troncature: il massimo osservato è 4.232 token totali per Clef e 3.850 token di stato per Kev.

Il dettaglio interessante è che **Clef ordina internamente le opzioni**. L'inversione nel JSON non arriva al modello come inversione dei token. Nel nostro pannello le 72 richieste corrispondono a 30 sequenze distinte per Clef e 60 per Kev; anche scambiare due letture identiche produce un duplicato.

Questo precisa il risultato pubblicato il 20 novembre: zero cambiamenti di Clef all'inversione delle opzioni non dimostrano, da soli, robustezza a quell'inversione. Il modello non vedeva una perturbazione effettiva di quel tipo. Conserviamo il dato osservato, correggendo l'interpretazione che potrebbe suggerire.

## La coppia deve conservare anche l'incertezza

Abbiamo congelato input, annotazioni e regola combinata prima di osservare le nuove risposte. Il combinatore distingue accordo sull'approfondimento, accordo sulla sua assenza, disaccordo, astensione singola, astensione doppia e incompletezza tecnica.

La regola conservativa candidata richiede approfondimento se almeno un modello lo propone o si astiene. Un errore tecnico resta un esito incompleto. Confronteremo questa scelta con i due modelli singoli e con la regola che approfondisce ogni divergenza testuale, senza mediare automaticamente le confidence.

Il prossimo passo è l'inferenza sul pilota congelato, verificando nuovamente gli input nel runtime effettivo. Non abbiamo ancora nuovi risultati Clef/Kev, né attivato questi giudici nella pipeline. Oggi abbiamo reso più controllabile la domanda che porremo loro, e meno facile confondere una ripetizione con una prova nuova.
