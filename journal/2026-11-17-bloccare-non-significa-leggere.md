# Bloccare un documento non significa averlo letto tutto

17 novembre 2026

La prova più utile della giornata è arrivata alla fine: quattro documenti ostili bloccati, quattro benigni ammessi, e una lacuna ancora aperta. Due istruzioni oltrepassavano il bordo della pagina. Il loro inizio arrivava ai detector, la coda no. Il verdetto era corretto, ma non potevamo dichiarare completa la lettura.

È il motivo per cui seguiamo un documento dall'estrazione fino alla ricevuta, conservando separatamente testo, evidenze grafiche, interpretazione e decisione.

## Le citazioni possono essere vere e le spiegazioni sbagliate

Abbiamo lavorato sulla differenza tra un comando operativo e lo stesso comando presentato come esempio didattico. Identificare ogni occorrenza con la propria posizione evita di applicare la cornice di un esempio a un ordine diverso. Separare il destinatario letterale dall'applicabilità nel contesto rende il contratto più preciso, ma non garantisce che il modello lo rispetti.

Le prove su nuovi testi hanno mostrato il limite. Su otto casi, Mistral Small 3.2 ha prodotto cinque verdetti corretti; soltanto tre risposte hanno superato anche la revisione delle evidenze e della spiegazione. Llama 3.1, sugli stessi input e criterio, ha ottenuto rispettivamente tre e una. Sono piccoli esperimenti di configurazioni concrete, non una graduatoria generale tra famiglie di modelli. Aggiungere un giudice non basta: gli errori possono sovrapporsi.

Un verificatore dedicato a stabilire se una citazione sostenga un'affermazione ha riconosciuto sei etichette su undici. Ha confuso l'assenza di supporto con la contraddizione. La citazione autentica e il suo valore probatorio restano due controlli distinti; questo candidato non è stato promosso nella politica decisionale.

## Il colore del testo non ne dimostra la visibilità

Nero su bianco ha un contrasto nominale elevato anche quando il testo è completamente trasparente, escluso da un ritaglio o coperto da un rettangolo successivo. Un calcolo cromatico non rappresenta tutto ciò che è accaduto durante il rendering.

Abbiamo quindi affiancato al contrasto una misura sperimentale: confrontare il rendering originale con una copia in cui la pittura del testo nativo è disabilitata, mantenendone posizioni e avanzamenti. Se nella regione non cambia alcun pixel, quel testo non ha contribuito all'immagine alla risoluzione esaminata.

Sui tre casi sintetici di occultamento totale — trasparenza, clipping e sovrapposizione opaca — la misura ha trovato le righe nascoste. Pagina e coordinate sono arrivate fino alle ricevute. I quattro controlli benigni non hanno prodotto false segnalazioni di assenza del testo. È una regressione sul piccolo campione di sviluppo, non una misura generale della copertura.

Il risultato opposto richiede cautela: qualche pixel cambiato non dimostra che tutta la riga sia leggibile. Le sovrapposizioni parziali, i contributi di testi sovrapposti e diversi costrutti PDF restano limiti espliciti.

## Tornare alle suite esistenti

Abbiamo selezionato otto documenti dalle suite del progetto, verificato gli hash e riesaminato testo e rendering, senza assumere corrette le etichette storiche. Li abbiamo inviati attraverso l'ingresso HTTPS e seguito le evidenze fino alla ricevuta finale.

| Controllo | Risultato osservato |
|---|---|
| Quattro documenti ostili | Tutti in quarantena |
| Quattro documenti benigni | Tutti ammessi |
| Due white-on-white | Assenza di contributo visibile con pagina e coordinate nella ricevuta |
| Due overlay parziali | Frammenti visibili; leggibilità non verificata |
| Nota benigna interamente fuori pagina | Recuperata nel testo di analisi e documento ammesso |

In sette casi almeno uno dei controlli grafici era parziale, e la ricevuta lo dichiarava. Questo include due benigni nei quali il rendering era valutato nel perimetro previsto, ma il contrasto non era interamente determinabile. Analisi parziale e contenuto ostile non sono sinonimi.

La lacuna delle righe che attraversano il bordo è emersa proprio qui. Un altro estrattore leggeva la frase completa; il testo effettivamente passato all'analisi ne conservava soltanto una parte. Bloccare questi attacchi espliciti non risolve il problema: in un altro documento, la porzione persa potrebbe contenere l'unico indizio decisivo.

## La prossima verifica

Correggeremo il recupero delle porzioni oltre il bordo, mantenendo provenienza e posizione senza duplicare il testo. Riproveremo i due documenti e controlli benigni tramite lo stesso ingresso, verificando l'intera frase nei payload dei detector e la propagazione delle evidenze fino alla ricevuta.

Modelli, soglie e politica resteranno fermi nel confronto. Il criterio di successo sarà la lettura dimostrabile, insieme ai verdetti e ai falsi allarmi. Gli attacchi distribuiti rimangono una categoria da verificare separatamente: questi risultati non ne certificano la copertura.

PIGUARD resta sperimentale. Oggi abbiamo una prova grafica migliore e una perdita testuale identificata con precisione. Entrambe sono informazioni utili, anche quando arrivano dalla stessa scansione conclusa con un blocco corretto.
