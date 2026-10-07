# EmbeddingGemma 2 e BGE-M3: somiglianza e intento non coincidono

7 ottobre 2026 — Il percorso, puntata 4

Nel [precedente articolo](2026-10-07-confrontare-modelli-senza-confondere-le-prove.md)
avevamo scaricato EmbeddingGemma 2 e preparato il confronto con BGE-M3. Ora
abbiamo completato un primo esperimento testuale. Non è una scelta definitiva:
cerchiamo di capire quali segnali producano i due modelli e dove possano ingannare
una politica che trasformi la somiglianza in rischio.

## Il banco di prova

Abbiamo fissato 18 testi sintetici prima delle inferenze: quattro riferimenti
di attacco, quattro parafrasi operative, quattro negazioni, quattro citazioni
didattiche e due descrizioni innocue. Due gruppi sono in italiano e due in inglese.
Non sono 18 PDF né un corpus indipendente: è un piccolo banco di sviluppo.

Per ciascun riferimento chiediamo: la parafrasi dell'attacco ottiene una
somiglianza maggiore della negazione? E della citazione didattica?
Non applichiamo soglie di blocco e non produciamo verdetti sui documenti.

| Configurazione | BGE-M3 | EmbeddingGemma 2 |
|---|---|---|
| Input | Solo testo | Solo testo, encoder audio/visivo esclusi |
| Precisione | FP32 | BF16 |
| Dimensioni | 1.024 | 768 |
| Prefisso | Nessuno | SentenceSimilarity su entrambi i lati |
| Parametri caricati | Circa 568 milioni | Circa 271 milioni |

Esecuzione su A40, con Transformers 5.19.0, SentenceTransformers 6.1.0,
Hugging Face Hub 1.33.0 e PyTorch 2.11.0 CUDA 12.8, in ambiente isolato.
Le vecchie librerie non riconoscevano l'architettura di Gemma 2: abbiamo
preparato il runtime del test senza aggiornare quello dei servizi attivi.

Checkpoint fissati: BGE-M3 `5617a9f61b028005a4858fdac845db406aefb181`;
EmbeddingGemma 2 `914f7f89142e33e77833254d9c9b90c3cef7303b`.
Dimensioni e hash LFS dei file scaricati verificati. Vettori finiti,
rinormalizzazione finale in FP32 e controllo esplicito dei token per evitare
troncamenti. Un primo calcolo con arrotondamento BF16 è conservato separatamente;
i risultati sotto usano la coseno dopo rinormalizzazione FP32.

## Il risultato osservato

| Ordinamento rispetto al riferimento d'attacco | BGE-M3 | EmbeddingGemma 2 |
|---|---:|---:|
| Parafrasi d'attacco sopra la negazione | 4 su 4 | 2 su 4 |
| Parafrasi d'attacco sopra la citazione didattica | 3 su 4 | 1 su 4 |

In questo banco BGE-M3 separa meglio questi casi secondo il criterio scelto.
Ma non sono accuracy, falsi positivi o falsi negativi di un detector.

Le citazioni ripetono letteralmente il riferimento, mentre le parafrasi cambiano
le parole. Un modello di somiglianza può correttamente considerare la citazione
molto vicina all'attacco. Il risultato non dimostra un difetto generale di Gemma:
mostra il limite di usare la sola vicinanza semantica per decidere se un testo
stia impartendo un comando o parlandone.

Neppure confrontiamo direttamente le scale numeriche dei due modelli. Una
coseno più alta in Gemma non significa automaticamente maggiore rischio rispetto
a BGE. Indici e soglie devono essere separati e calibrati sul compito.

## Tempi, senza trasformarli in una classifica generale

Tre passaggi per modello sugli stessi 18 testi, con batch di otto:

| Modello | Prima passata | Seconda | Terza |
|---|---:|---:|---:|
| BGE-M3 | 255 ms | 43,3 ms | 43,5 ms |
| EmbeddingGemma 2 | 298 ms | 118,2 ms | 118,3 ms |

Sono tempi dell'intero insieme, a modello già caricato, non tempi per documento.
Precisioni e architetture differiscono e abbiamo soltanto due passaggi caldi:
non è un benchmark generale di throughput. Anche i caricamenti non sono
confrontabili come misura del disco, perché collocazione dei file e cache
differivano. Più piccolo non significa automaticamente più veloce nel nostro
specifico runtime.

## Cosa facciamo dopo

Aggiornamento del 7 ottobre: i controlli FP32 e retrieval sugli stessi 18 testi
sono stati completati; risultati e limiti nella [puntata 5](2026-10-07-json-valido-reasoning-incompleto.md).
Il piano seguente documenta il punto di partenza; il corpus ampliato resta da realizzare.

Il prossimo confronto userà anche il compito asimmetrico query/documento,
con corpus più ampio e controlli difficili bilanciati. Verificheremo Gemma in
FP32 per distinguere l'effetto della precisione da quello del modello.
Calibrazione e valutazione dovranno poi usare insiemi separati.

La parte visiva resta da provare. Non abbiamo ancora misurato il recupero di
attacchi sparsi dentro documenti lunghi, né il beneficio incrementale nella
politica finale. Il test non modifica BGE-M3, gli indici o le soglie live.

Per PIGUARD il punto resta questo: gli embedding aiutano a trovare relazioni;
la valutazione contestuale deve distinguere uso, citazione e negazione, conservando
le evidenze. Nessun filtro di somiglianza deve far sparire parti del documento
dalla copertura dell'analisi.

Fonti delle specifiche, distinte dai nostri risultati: [BGE-M3](https://huggingface.co/BAAI/bge-m3)
e [EmbeddingGemma 2](https://ai.google.dev/gemma/docs/embeddinggemma/model_card_2).
Gli artefatti del laboratorio restano privati: questa è una sintesi metodologica,
non un benchmark integralmente riproducibile dal solo articolo.
