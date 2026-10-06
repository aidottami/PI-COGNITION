# PI-COGNITION — Engineering Journal

Stiamo costruendo **PIGUARD**, una pipeline sperimentale per analizzare documenti
e individuare tentativi di prompt injection prima che il loro contenuto venga
utilizzato da sistemi di intelligenza artificiale.

Una prompt injection cerca di far trattare come istruzioni autorevoli ciò che
dovrebbe restare un dato: il testo di un documento, una pagina recuperata o il
risultato di uno strumento. Riconoscerla richiede più di una lista di parole
sospette. La stessa frase può essere un comando operativo, una citazione didattica
o il racconto di un incidente.

**PI-COGNITION è il diario pubblico di questo lavoro**, non il repository del
servizio né una dichiarazione di efficacia certificata. Raccontiamo lo scopo,
le scelte architetturali, gli esperimenti, gli errori e ciò che resta da capire.
L’impostazione editoriale è la stessa di [HC — Engineering Journal](https://github.com/aidottami/HC-JOURNAL).

## Articoli

| Data | Articolo | Tema |
|---|---|---|
| 6 ottobre 2026 | [Dalla classificazione alle evidenze: perché una risposta valida non basta](journal/2026-10-06-dalla-classificazione-alle-evidenze.md) | Il percorso, puntata 1 — architettura, primi confronti, attacchi distribuiti e problemi del runtime |

## Che cosa vogliamo costruire

L’architettura di riferimento ha sei elementi. È la direzione del progetto,
non una promessa che tutte le integrazioni siano già completate e validate.

1. **Un unico ingresso autenticato**, con accessi controllati.
2. **Preparazione del documento con copertura verificabile**: estrazione,
   OCR quando necessario, provenienza e segnalazione esplicita delle parti mancanti.
3. **Detector con responsabilità distinte**, dei quali conservare contributi,
   versioni, errori e limiti.
4. **Valutazione contestuale tramite LLM**, capace di esaminare istruzioni,
   citazioni, definizioni e riferimenti distribuiti nel testo.
5. **Una sola politica decisionale versionata e testata**, che distingua rischio,
   disaccordo e analisi incompleta, senza delegare il verdetto a una stringa libera.
6. **Un risultato tracciabile**, con evidenze, motivazioni, copertura e limiti.

La preparazione del documento è pensata per worker isolati ed effimeri.
I modelli di inferenza possono invece restare residenti: isolamento della
preparazione e ciclo di vita dei modelli sono problemi differenti.

Studiamo il contributo di più modelli e di un ulteriore giudice con decisioni
strutturate, di tipo Jev-like. È una direzione da valutare, non un componente
già validato. Più giudici non garantiscono indipendenza: possono condividere gli
stessi errori. Pesi e soglie richiedono misure e calibrazione, non una media
delle confidence dichiarate dai modelli.

## La filosofia del lavoro

**Nessuna minaccia rilevata non significa documento sicuro.** Un contenuto non
letto, una richiesta scaduta o un detector indisponibile non devono trasformarsi
in un esito rassicurante. L’incompletezza va conservata nel risultato.

**Copertura, formato e correttezza sono proprietà diverse.** Aver elaborato tutti
i token non dimostra di aver compreso un attacco. Un JSON valido non dimostra
che la decisione sia giusta. Una citazione autentica non rende vera ogni
interpretazione che il modello le attribuisce.

**Misuriamo per caso d’uso.** Un filtro su una frase breve, un documento lungo,
l’estrazione OCR e la generazione di una risposta chatbot non sono lo stesso
benchmark. Distinguiamo casi unici e ripetizioni, falsi positivi e falsi negativi,
caricamento iniziale, latenza ordinaria e picchi.

**Gli attacchi distribuiti fanno parte del problema fin dall’inizio.** Una
definizione apparentemente innocua può acquistare significato operativo quando
richiamata molte pagine dopo. Stiamo costruendo e verificando un percorso di
analisi locale dei blocchi e correlazione globale, senza assumere che basti
selezionare i frammenti con lo score più alto.

**I tentativi falliti sono risultati da conservare.** Documentiamo ciò che si è
fermato, perché e quali conclusioni non possiamo trarne. Una correzione apre una
nuova verifica; non rende retroattivamente riuscita quella precedente.

## Come procediamo

Partiamo da una domanda concreta, fissiamo il comportamento atteso e la
configurazione, eseguiamo una prova circoscritta e registriamo esiti e anomalie.
Prima di integrare un candidato nella pipeline, verifichiamo anche i percorsi
di errore: timeout, input troppo grandi, risposte incomplete e risorse occupate.

Confrontiamo i risultati con controlli benigni, prestando attenzione all’uso
legittimo di citazioni ostili. I casi sintetici aiutano a isolare un meccanismo,
ma non sostituiscono un corpus indipendente e rappresentativo. Le etichette
proposte da chi genera un documento non costituiscono da sole una verità verificata.

Quando ricerche esterne informano una scelta, citeremo le fonti e distingueremo
le affermazioni degli autori dalle prove riprodotte da noi. Prompt injection,
jailbreak e poisoning restano categorie da distinguere, non etichette intercambiabili.

## Stato e limiti

Il progetto è sperimentale. Abbiamo osservato sia attacchi non riconosciuti sia
contenuti leciti bloccati. Non abbiamo una percentuale generale di copertura
difendibile né una dimostrazione completa di prontezza per l’uso pubblico.

Il filtro rapido davanti a un chatbot è un caso d’uso aggiuntivo in studio:
i suoi risultati non certificano la qualità del chatbot o la scansione dei PDF.
Anche correlazioni fra documenti, memoria persistente e contesti applicativi
richiedono verifiche ulteriori rispetto all’analisi di un singolo file.

## Pubblicazione e correzioni

Questo repository contiene **materiale editoriale pubblico**. Non pubblichiamo
credenziali, topologia dell’infrastruttura, dati privati dei documenti o log grezzi
del laboratorio. Le sintesi quantitative indicano metodo e limiti; non sono
presentate come benchmark integralmente riproducibili quando gli artefatti non
sono pubblici.

Gli articoli hanno una storia versionata. Correzioni sostanziali e sviluppi
successivi saranno indicati esplicitamente. Manterremo distinta la data della
prova da quella della pubblicazione, senza retrodatare risultati nuovi.
