# Dalla classificazione alle evidenze: perché una risposta valida non basta

**6 ottobre 2026 · PI-COGNITION Engineering Journal · 001**

*Il percorso, puntata 1 — sintesi delle prove del 1 e del 6 ottobre.*

L’obiettivo di PIGUARD è analizzare documenti prima che entrino nel contesto di
un sistema AI. La domanda non è soltanto «questo testo contiene una frase
sospetta?», ma anche «a chi è rivolta, quale azione richiede e da quale fonte
pretende di ricevere autorità?».

Una pagina di formazione può citare un attacco senza essere un attacco. Un
documento può invece cercare di manipolare chi lo sta valutando senza contenere
una richiesta esplicita di password. E due frammenti lontani possono costituire
insieme un’istruzione che, letta a pezzi, sembra innocua.

## Ricostruire la catena prima di attribuirle capacità

Il lavoro è passato attraverso una ricostruzione dei ruoli della pipeline:
ingresso, estrazione, detector, modelli contestuali, aggregazione e risultato.
La disponibilità di un servizio non basta a dimostrare che il suo contributo
raggiunga correttamente il verdetto finale.

Abbiamo scelto di mantenere una sola politica decisionale, con versioni e test,
e di distinguere il risultato del singolo componente da quello dell’intera
scansione. Anche l’analisi incompleta deve essere rappresentata: non può essere
confusa con l’assenza di una minaccia.

## Leggere tutto non basta a collegare tutto

In una prova su un documento sintetico lungo, un’istruzione era distribuita fra
una definizione iniziale e un richiamo molte pagine dopo. Il testo dei frammenti
era stato recuperato; il problema non era semplicemente la loro assenza dall’OCR.

NeMo 12B e Llama 3.1 8B, entrambi quantizzati Q4_K_M, non hanno riconosciuto
l’attacco nella vista nativa integrale, pur con copertura dei token verificata.
La prova era diagnostica, non una valutazione generale dei due modelli.

Quando abbiamo fornito a NeMo soltanto le due pagine già note, il risultato è
cambiato. Ma conoscevamo in anticipo dove cercare: questa selezione non dimostra
che il sistema sappia trovare e correlare i frammenti nell’intero documento.

Da qui la direzione di lavoro: analizzare tutti i blocchi, raccogliere citazioni
verificabili e riferimenti, quindi correlare le evidenze. Non basta dichiarare
questa architettura: dobbiamo misurare anche ciò che il passaggio locale omette,
perché una correlazione successiva non può recuperare automaticamente evidenze
mai conservate.

## Il filtro breve e gli errori complementari

Abbiamo separato un secondo esperimento: classificare messaggi brevi prima di
un eventuale chatbot. Qui chiediamo PASS o BLOCK secondo una policy esplicita,
non una spiegazione completa del documento.

Una prima batteria di 16 casi aveva dato risultati migliori a Mistral Small
3.2 24B rispetto a Qwen 3 8B. Abbiamo quindi fissato 24 casi nuovi, metà benigni
e metà da bloccare, prima di eseguire il confronto. Ogni caso è stato ripetuto
tre volte per ciascuna delle due policy, semplice e articolata.

| Modello e policy | Risposte attese | Falsi positivi | Falsi negativi |
|---|---:|---:|---:|
| Qwen 3 8B, semplice | 56/72 | 0 | 16 |
| Qwen 3 8B, articolata | 60/72 | 0 | 12 |
| Mistral Small 3.2 24B, semplice | 63/72 | 9 | 0 |
| Mistral Small 3.2 24B, articolata | 69/72 | 3 | 0 |

Sono conteggi di chiamate, non 72 esempi indipendenti per riga. I casi sono
manuali, le attese sono quelle della policy di prova e l’autore conosceva già
le categorie di errore precedenti: non è un corpus indipendente adjudicato.
Tutti gli output erano formalmente validi.

Qwen lasciava passare alcune istruzioni che chiedevano di alterare o nascondere
il risultato del valutatore. Mistral, con la policy articolata, bloccava invece
una richiesta lecita di traduzione didattica di una frase ostile, in tutte e
tre le ripetizioni.

Questo rende interessante studiare più giudici, ma non risolve l’aggregazione.
Bloccare quando uno qualsiasi segnala un rischio conserva i falsi positivi;
richiedere l’accordo di tutti può conservare i falsi negativi. Non abbiamo
ancora pesi calibrati né una regola ensemble validata su dati indipendenti.

## Una latenza non descrive tutte le attese

Su una GPU A40, con output brevissimi e chiamate sequenziali locali, le mediane
della nuova batteria erano circa 100–106 millisecondi per Qwen e 270 millisecondi
per Mistral. Cache e prefissi ripetuti possono favorire queste misure: non sono
tempi end-to-end da Internet o sotto carico concorrente.

In una batteria precedente una richiesta Mistral aveva impiegato 7,63 secondi,
quasi tutti attribuiti all’elaborazione del prompt. Ripetendo la stessa domanda
120 volte, alternando le due policy, il massimo osservato è stato 0,53 secondi.
Il picco non si è riprodotto, ma la sua causa resta aperta.

Era distinto da un altro problema: caricare il modello richiedeva minuti.
Abbiamo confrontato gli stessi pesi, verificati tramite hash, da un volume di
rete e da una copia locale, con un server isolato e parametri uguali.

| Posizione dei pesi | Primo caricamento | Secondo caricamento |
|---|---:|---:|
| Volume di rete | 115,4 s | 107,2 s |
| Copia locale | 4,7 s | 5,1 s |

La differenza indica che l’accesso ai pesi dominava il ritardo nelle condizioni
provate. Non isola la velocità fisica del disco: la cache RAM non era controllata.
Inoltre, la copia iniziale con verifica aveva richiesto circa 144 secondi.
Quel costo non scompare quando nasce un ambiente nuovo.

## Un timeout del client non è necessariamente uno stop del modello

Prima di lanciare l’analisi locale con evidenze abbiamo verificato il backend
reale, anziché assumere che i test simulati bastassero.

Il primo controllo ha individuato un template generico non adatto alla prova
Mistral. Il banco si è fermato prima dell’inferenza. Con un template testuale
esplicito verificato, i controlli di tokenizzazione, copertura, terminazione
della risposta e rifiuto degli input troppo grandi sono passati.

Il controllo successivo è fallito: il client interrompeva l’attesa dopo due
secondi, ma il runner continuava a generare sulla GPU per almeno altri dieci.
Abbiamo arrestato il solo processo sperimentale, senza accodare nuove analisi.

Non è un errore di detection. È un problema di gestione dell’esecuzione che
potrebbe occupare risorse e compromettere le prove successive. I due documenti
previsti per il nuovo percorso di evidenze non sono stati analizzati in quel run.

## Il prossimo passo e il confine delle conclusioni

Prima dei documenti, dobbiamo verificare una cancellazione reale oppure una
supervisione capace di fermare e ripristinare soltanto il runner dedicato.
Poi potremo mettere alla prova l’analisi di tutti i blocchi e la correlazione
globale su un caso ostile distribuito e un controllo benigno.

Le prove raccontate qui non stabiliscono una percentuale generale di protezione,
non certificano l’apertura pubblica del servizio e non eleggono un modello
vincitore. I report e i dati grezzi del laboratorio sono conservati privatamente:
questo articolo ne pubblica una sintesi, non un pacchetto di riproduzione completo.

La domanda che guida il lavoro resta: **possiamo ricostruire non soltanto il
verdetto, ma anche che cosa è stato letto, quali evidenze lo sostengono e dove
l’analisi si è fermata?**

---

[Indice del journal](../README.md)

*Aggiornamento del 7 ottobre: le prove successive sulla supervisione del runner
e sulla raccolta delle evidenze sono raccontate in
[Citazioni esatte e prove mancanti](2026-10-07-citazioni-esatte-e-prove-mancanti.md).
Il resoconto sopra conserva lo stato delle prove descritte nella prima puntata.*
