# Citazioni esatte e prove mancanti

**7 ottobre 2026 · PI-COGNITION Engineering Journal · 002**

*Il percorso, puntata 2 — prove del 6 e del 7 ottobre.*

Un modello può restituire citazioni autentiche e perdere proprio il passaggio
che serviva a capire un attacco. Nei nuovi esperimenti di PIGUARD abbiamo
osservato entrambe le cose: un miglioramento della validità formale delle
risposte e una regressione nella raccolta di un’evidenza importante.

Per questo non abbiamo promosso il nuovo candidato. Il risultato utile di
questa fase è aver distinto meglio tre domande: il modello rispetta il formato,
cita davvero il documento e raccoglie ciò che serve a interpretarlo?

## Prima rendere controllabile l’esecuzione

La [puntata precedente](2026-10-06-dalla-classificazione-alle-evidenze.md)
si fermava su un problema del runtime: la scadenza del client non arrestava
necessariamente la generazione sulla GPU.

Abbiamo verificato un supervisore che, nel banco isolato, arresta e ripristina
il solo runner dedicato prima di consentire nuove richieste. La richiesta
scaduta resta fallita e non viene rilanciata automaticamente. Non è una
correzione della cancellazione nativa né una soluzione già validata per un
server condiviso, ma ha consentito di riprendere le prove sequenziali.

## Chiedere al modello di ricopiare non garantisce una citazione

Il primo protocollo chiedeva al modello di riportare il testo dell’evidenza
e le posizioni esatte di inizio e fine nel blocco originale.

In una prova limitata, con formato JSON vincolato, tre risposte complete
contenevano complessivamente 30 frammenti: nessuno corrispondeva al testo
originale nelle posizioni dichiarate. Soltanto quattro testi citati comparivano
letteralmente da qualche parte nel blocco attribuito. La quarta risposta era
stata interrotta dal limite di generazione.

Non era quindi soltanto un problema di contare caratteri. Il formato poteva
essere valido mentre la citazione non lo era. Il validatore ha respinto le
risposte; non abbiamo ricostruito posizioni plausibili o corretto le citazioni
con somiglianze approssimative.

## Far scegliere i frammenti invece di farli riscrivere

Abbiamo spostato il lavoro meccanico nel codice. Il testo viene suddiviso in
unità con identificatori deterministici, senza eliminare o normalizzare
caratteri. Il modello seleziona gli identificatori; il codice recupera testo,
posizioni e provenienza dagli originali.

Una prima versione ha prodotto una risposta valida su quattro. Restavano
errori di ordine, selezioni del solo contesto vicino e output troncati.

Nella versione successiva abbiamo separato gli identificatori del blocco da
analizzare da quelli del contesto, trattandoli come insiemi senza ordine.
L’ordinamento è diventato responsabilità del codice: una scelta esplicita del
nuovo contratto, non una riparazione retroattiva degli output precedenti.

Infine abbiamo vincolato lo schema di ciascuna richiesta agli identificatori
ammessi nei due gruppi. Non basta chiedere di scegliere dal gruppo corretto:
il formato di generazione può restringere concretamente le scelte disponibili.
La pertinenza della scelta resta però una questione diversa.

## Che cosa hanno mostrato le prove limitate

Abbiamo usato Mistral Small 3.2 24B, quantizzato Q4_K_M, su GPU A40. Ogni
variante della tabella prevedeva due richieste per ciascuno di due PDF, un caso
avversariale e un controllo benigno. I target erano presi in ordine dal
manifesto di estrazione, non selezionati perché già noti come sospetti.
Il budget massimo di risposta era di 2.048 token.

| Protocollo | Risposte formalmente accettate | Problemi nelle altre risposte |
|---|---:|---|
| Testo e posizioni generati dal modello, con schema JSON | 0/4 | Citazioni non esatte e troncamento |
| Selezione di unità con identificatori | 1/4 | Ordine, provenienza dal solo contesto e troncamento |
| Selezioni separate per blocco e contesto | 0/4 | Identificatori nel gruppo sbagliato e troncamento |
| Selezioni separate con identificatori ammessi vincolati | 2/4 | Due risposte troncate |

Sono prove diagnostiche piccole, su input riutilizzati: non sedici documenti
indipendenti e non una percentuale di detection. Fra alcuni protocolli cambiano
prompt e struttura della risposta; la tabella non isola l’effetto causale di
ogni modifica. Nessuna variante ha completato la scansione dei due PDF o
prodotto un nuovo verdetto globale.

## La regressione nascosta da un formato migliore

Il confronto delle risposte ha individuato un problema più importante del
conteggio dei JSON validi.

Nel primo blocco del documento avversariale era presente una definizione
rilevante per un comportamento di alterazione dell’esito dell’analisi.
La prima versione con identificatori aveva selezionato i due frammenti che
la contenevano, riconoscendola come definizione.

La nuova versione con identificatori vincolati ha invece raccolto normali
descrizioni dell’audit e omesso entrambi quei frammenti. Le citazioni restituite
erano autentiche, ma mancava un’evidenza essenziale.

È una regressione locale osservata in questo confronto, non una misura del
tasso di falsi negativi sull’intero documento o una conclusione generale sul
modello. Basta però a impedire che il miglioramento formale venga presentato
come un miglioramento della rilevazione.

Per gli attacchi distribuiti questa distinzione è decisiva: se l’analisi locale
non conserva una definizione, la correlazione successiva può non ricevere il
materiale necessario a interpretare un richiamo lontano.

## Il prossimo controllo riguarda ciò che manca

La suite software arriva a 180 test superati. Verifica contratti, provenienza,
confini del contesto e comportamenti d’errore: non dimostra la capacità del
modello di riconoscere 180 attacchi.

Il passo successivo è costruire una verifica delle evidenze essenziali:
definizioni indirette, riferimenti, contenuti innocui simili e casi nuovi non
usati per adattare il prompt. Misureremo anche le omissioni, non soltanto la
validità delle citazioni prodotte. Il troncamento resta un problema aperto;
aumentare il budget non dimostrerebbe da solo di aver risolto la selezione.

La correlazione globale resta esclusa da queste prove e la pipeline attiva
non è stata modificata. Conserviamo privatamente input, risposte e configurazioni;
questa è una sintesi del laboratorio, non un benchmark integralmente
riproducibile con gli artefatti pubblicati.

**Una prova autentica può essere irrilevante. Una risposta formalmente corretta
può essere incompleta. Il controllo deve riguardare anche ciò che il modello
non ha portato all’attenzione del decisore.**

---

[Puntata precedente](2026-10-06-dalla-classificazione-alle-evidenze.md) · [Indice del journal](../README.md)
