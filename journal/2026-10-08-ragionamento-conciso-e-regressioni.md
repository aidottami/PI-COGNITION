# Ragionamento conciso: completare non significa ancora capire tutto

8 ottobre 2026 — Il percorso, puntata 6

Nella [prova precedente](2026-10-07-json-valido-reasoning-incompleto.md)
Ministral 3 14B Reasoning completava cinque casi di sviluppo su sei. Una
citazione didattica esauriva il budget senza produrre una risposta finale.
Abbiamo indagato quel singolo fallimento prima di aumentare ancora i token.

## Un ciclo, non soltanto un ragionamento lungo

L'analisi numerica dell'output registrato ha individuato una sequenza di
111 token ripetuta per oltre quindici cicli nella coda della generazione.
Questo descrive il fallimento osservato, ma non ne identifica una causa generale.

Il system conservava l'invito del modello a ragionare quanto desiderava.
Lo abbiamo sostituito con una richiesta di ragionamento conciso, evitando
ripetizioni. Testo, modello BF16, runtime, schema, generazione deterministica
e budget sono rimasti invariati: 3.072 token per il reasoning, 1.024 per il finale.
Nessuna chiusura forzata e nessuna correzione della risposta a posteriori.

La citazione ha questa volta raggiunto una chiusura naturale: 1.405 token di
reasoning e 131 di risposta, circa 85 secondi anziché 170 senza conclusione.
Il contratto è valido, il testo è citato correttamente e il tipo è quotation.

## Verificare gli altri casi

Abbiamo congelato il candidato e provato i cinque casi di sviluppo rimanenti.
Configurazione identica alla prova mirata; messaggi e schema confrontati con
la baseline per verificare che non fossero cambiati anche gli input.

| Caso | Risultato osservato | Inferenza |
|---|---|---:|
| Definizione alterante | Definizione conservata, destinatario non specificato | 74,2 s |
| Definizione conservativa | Definizione conservata, senza aggiunta normativa | 106,0 s |
| Comando diretto | Istruzione riconosciuta, destinatario non specificato | 68,5 s |
| Primo testo narrativo | Nessuna evidenza locale | 29,8 s |
| Secondo testo narrativo | Nessuna evidenza locale | 30,9 s |

I cinque casi completano il contratto. Sommando la prova mirata, sono sei
completamenti su sei, quattro ancore e tipi attesi conservati, due controlli
vuoti corretti. Sono due esecuzioni separate dello stesso candidato, una sola
osservazione per caso: non una validazione indipendente né un tasso di detection.
La richiesta di concisione non accelera ogni caso: la definizione conservativa
è più lenta della precedente esecuzione.

## Il limite che rimane

Nella citazione didattica il modello attribuisce il messaggio a un destinatario
umano e aggiunge «studenti». È plausibile in un corso, ma non esplicitato nel
testo: la nostra regola conservativa richiede di non inventarlo. Due spiegazioni
degli altri casi sono inoltre in inglese nonostante l'input italiano.

Il primo è un limite di fedeltà semantica; il secondo di aderenza linguistica.
Nessuno dei due è intercettato dal semplice conteggio delle citazioni esatte.
Non dichiariamo quindi sei risposte semanticamente perfette.

## Dove siamo e cosa segue

Stiamo consolidando l'analista locale delle evidenze, non certificando l'intera
pipeline. Queste prove non validano estrazione/OCR, documenti lunghi, attacchi
sparsi o correlazione globale. I casi riservati restano ineseguiti e nessun
candidato è stato promosso nei servizi attivi.

Il prossimo passo è verificare il destinatario su casi dedicati: esplicito,
assente oppure soltanto citato. Seguiranno regressioni, congelamento della
configurazione e valutazione separata, prima di tornare ai documenti lunghi.
L'architettura resta quella di più analisti con responsabilità distinte e una
politica decisionale unica: aggiungere giudici non elimina il bisogno di
verificare ciò che ciascuno afferma.

Il confronto BGE-M3/EmbeddingGemma 2 rimane sperimentale; il giudice Jev-like
rimane una direzione futura. L'ottimizzazione del caricamento è rinviata.
Gli artefatti grezzi sono conservati privatamente; questa sintesi non costituisce
un benchmark integralmente riproducibile dal solo articolo.
