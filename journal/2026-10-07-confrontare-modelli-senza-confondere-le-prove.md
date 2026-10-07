# Confrontare modelli senza confondere le prove

7 ottobre 2026 — Il percorso, puntata 3

Aggiornamento successivo: [primo confronto testuale EmbeddingGemma 2/BGE-M3](2026-10-07-embeddinggemma2-bge-somiglianza-e-intento.md).
Lo stato preparatorio raccontato qui resta il checkpoint precedente alle prove.

Nel [precedente aggiornamento](2026-10-07-citazioni-esatte-e-prove-mancanti.md)
abbiamo incontrato una regressione importante: citazioni formalmente valide
non garantivano la conservazione di una definizione decisiva. Oggi abbiamo
ridotto il problema a un banco sintetico e iniziato a confrontare candidati.

## Sei testi, non 192 documenti

Il banco di sviluppo contiene sei testi brevi: due definizioni che prescrivono
rispettivamente alterazione o conservazione dei risultati, un comando, una
citazione didattica e due descrizioni innocue. Quattro ulteriori casi restano
riservati, inclusi richiami distanti. Sono casi autoriali, non un corpus cieco
indipendente. I 192 test software passati verificano il codice: non sono 192
documenti analizzati dal modello.

Con Mistral Small 3.2 24B quantizzato, il primo prompt conserva tutte e quattro
le evidenze attese, ma tipizza la citazione come istruzione e produce
un'osservazione indebita per uno dei due controlli benigni.

Una variante esplicita meglio uso e menzione, quando non produrre evidenze e
come spiegare una definizione. Nella successiva esecuzione corregge entrambi
gli errori, senza perdere le quattro ancore. Rimane però una sovrainterpretazione
del destinatario, attribuito a un sistema AI senza un'indicazione esplicita.

Una prova per caso e variante non dimostra robustezza: abbiamo adattato il prompt
conoscendo questi esempi. I testi sono inoltre molto brevi; conservare l'intero
testo non prova capacità di selezione su documenti lunghi. La regressione
documentale precedente non è risolta da questo risultato.

## Un secondo modello: esecuzione riuscita, contratto non superato

Abbiamo eseguito gli stessi sei casi con Ministral 3 14B Reasoning in BF16,
usando Transformers. Il modello si carica ed esegue tutte le generazioni,
ma nessuna risposta supera il contratto: compaiono blocchi Markdown e, nelle
risposte con evidenze, insiemi con parentesi graffe invece degli array JSON.

È anche un problema del nostro esperimento: il primo runtime imponeva uno schema,
il secondo no; la parola «insiemi» nel prompt non precisava sufficientemente la
rappresentazione. Non ripariamo retroattivamente gli output per contarli come
successi. La configurazione del reasoning nativo resta inoltre da verificare:
non sono stati emessi segmenti di ragionamento nei sei output.

| Configurazione | Sei inferenze, somma | Caricamento separato |
|---|---:|---:|
| Mistral Small, baseline | 24,66 s | 85,81 s |
| Mistral Small, prompt candidato | 23,57 s | 76,43 s |
| Ministral Reasoning, pilot BF16 | 49,29 s | 290,41 s |

Misure su una GPU A40, singola esecuzione per caso. Runtime, precisione e
vincoli differiscono: non è una classifica controllata dei modelli né una stima
dei tempi di scansione PDF. Per Ministral, un caricamento preliminare interrotto
per correggere il nostro parser è escluso dalla riga e conservato nel laboratorio.

## EmbeddingGemma 2 entra nella sperimentazione

Stiamo avviando anche il confronto fra **EmbeddingGemma 2 e BGE-M3** per la
componente similarity. Stato effettivo: checkpoint ufficiale scaricato e
verificato; inferenza e benchmark comparativo non ancora eseguiti.

Google ha annunciato EmbeddingGemma 2 il 6 ottobre 2026: contesto 8K e supporto
multimodale modulare sono le novità che ci interessano. Non confondiamo questa
versione con EmbeddingGemma del 2025. [Annuncio Google](https://blog.google/innovation-and-ai/technology/developers-tools/embeddinggemma-2/)

Inizieremo dal testo, con vettori a 768 dimensioni, indici separati e soglie da
calibrare. La scheda prescrive BF16 o FP32, non FP16. In seguito valuteremo se
gli embedding delle pagine renderizzate aggiungano segnali utili al testo estratto.
[Scheda del modello](https://ai.google.dev/gemma/docs/embeddinggemma/model_card_2)

BGE-M3 offre anche retrieval sparse e multi-vettore; il percorso di similarity
esaminato nel nostro repository usa la componente densa. «Sparse retrieval»
non significa protezione dagli attacchi sparsi nei documenti.
[Scheda BGE-M3](https://huggingface.co/BAAI/bge-m3)

La somiglianza non è un verdetto: comandi, negazioni e citazioni possono risultare
vicini nello spazio vettoriale. Il confronto dovrà misurare il recupero degli
attacchi a parità di falsi allarmi e mantenere la copertura, senza usare gli
embedding come filtro che esclude parti del documento dall'analisi.

## La direzione resta quella dei giudici indipendenti

Vogliamo confrontare analisi indipendenti degli stessi contenuti, con evidenze
verificabili e una sola politica finale. Non basta mediare confidence e non
basta che due modelli concordino: possono condividere errori, soprattutto se
appartengono alla stessa famiglia. Un ulteriore giudice Jev-like resta una linea
di studio, distinta dalla ricerca di embedding migliori.

Prossimi passi: rendere affidabile il contratto del secondo analista, verificare
il suo reasoning nativo e preparare il confronto degli embedding. Nessun
candidato di questi esperimenti è stato promosso nella pipeline attiva.
