# JSON valido, reasoning incompleto: due problemi diversi

Aggiornamento dell'8 ottobre: la [puntata 6](2026-10-08-ragionamento-conciso-e-regressioni.md)
documenta la diagnosi del ciclo ripetitivo e la successiva prova controllata.

7 ottobre 2026 — Il percorso, puntata 5

Torniamo all'analisi delle evidenze documentali. Il nostro obiettivo non è
ottenere una risposta che sembri convincente: servono riferimenti verificabili
al testo, interpretazioni fedeli e un esito esplicito quando l'analisi non termina.

## Separare ragionamento e risposta finale

Abbiamo provato Ministral 3 14B Reasoning 2512 in BF16 su A40, con Transformers
5.17 e tokenizer nativo. Sei testi sintetici di sviluppo: due definizioni,
un comando, una citazione didattica e due descrizioni innocue. Non sono PDF,
né una valutazione indipendente o rappresentativa della prompt injection.

Il primo tentativo con reasoning nativo produceva cinque risposte concluse ma
racchiuse in Markdown, tutte rifiutate dal contratto; la sesta esauriva il budget.
Le sole istruzioni di formato non erano sufficienti.

Abbiamo quindi applicato una grammatica XGrammar alla sola risposta finale,
dopo la chiusura naturale del reasoning. Gli ID selezionabili sono quelli
presenti nella richiesta. Il validatore successivo resta invariato: il vincolo
di generazione non sostituisce la verifica delle citazioni.

Budget separati: 3.072 token per il reasoning e 1.024 per il finale. Nessuna
chiusura forzata e nessuna riparazione del JSON a posteriori.

## Il risultato e ciò che non risolve

Cinque risposte su sei superano ora il contratto. Nei tre casi con evidenze
completati, le ancore e i tipi attesi sono conservati; i due controlli narrativi
non producono osservazioni. La citazione didattica esaurisce invece tutti i
3.072 token di reasoning senza arrivare alla risposta finale.

La revisione semantica trova inoltre un destinatario umano attribuito senza
supporto nel testo e un'aggiunta speculativa su possibili implicazioni normative.
Sono errori che il conteggio delle citazioni corrette, da solo, non rileva.

Non diciamo quindi «cinque documenti classificati correttamente». Diciamo:
cinque contratti superati, un caso incompleto e limiti semantici ancora aperti.
Un'incompletezza non diventa un documento benigno.

Le sei inferenze richiedono complessivamente circa 9 minuti, caricamento escluso.
Il modello viene caricato in circa 202 secondi. È una sola esecuzione per caso;
prompt, budget e vincoli differiscono dalla prova precedente, quindi non è
un confronto causale del solo decoding. I casi riservati non sono stati eseguiti.
La suite software separata supera 192 test, senza certificare la qualità del modello.

## Aggiornamento sul confronto embedding

Dopo il [primo confronto](2026-10-07-embeddinggemma2-bge-somiglianza-e-intento.md),
abbiamo ripetuto gli stessi 18 testi con EmbeddingGemma 2 in BF16 e FP32,
sia con prefisso di similarità sia con query/documento asimmetrici.

La precisione non cambia gli ordinamenti osservati. In entrambe le precisioni,
la parafrasi d'attacco supera la negazione in 2 gruppi su 4. Rispetto alla
citazione didattica, passa da 1 su 4 nel compito simmetrico a 2 su 4 nel retrieval.
Restano risultati su un minuscolo banco di sviluppo, non tassi di detection.
Il prossimo confronto richiede testi più numerosi e sovrapposizione lessicale
bilanciata, non ulteriori adattamenti ai soli quattro gruppi.

## Priorità

Il caricamento lento resta un'ottimizzazione rinviata. Un precedente confronto
con un altro modello e runtime indicava un forte effetto dello storage/cache;
non attribuiamo automaticamente tutta la latenza attuale alla medesima causa.
I modelli della futura pipeline saranno residenti, non ricaricati per documento.

Prima di combinare i giudizi dobbiamo rendere affidabili i singoli analisti:
completezza, citazioni e interpretazione sono requisiti distinti. Aumentare
indefinitamente il budget non è una soluzione dimostrata. Nessun candidato
di queste prove è stato promosso nei servizi attivi.

Riferimento tecnico: [XGrammar, generazione JSON e JSON Schema](https://github.com/mlc-ai/xgrammar/blob/main/docs/defining_structures/json_generation.md).
Gli artefatti grezzi restano privati; questa è una sintesi metodologica, non un
benchmark integralmente riproducibile dal solo articolo.
