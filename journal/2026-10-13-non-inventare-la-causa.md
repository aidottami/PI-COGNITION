# Non inventare la causa di un fallimento

13 ottobre 2026

Un processo ucciso non dimostra, da solo, un esaurimento memoria. Oggi abbiamo
reso più rigoroso questo confine: il candidato acquisisce lo stato terminale
di un container soltanto con identità e codice di uscita coerenti. Se manca
la prova, la causa resta sconosciuta. L'adapter è testato con simulazioni;
non raccontiamo questi test come nuovi OOM provocati su Linux o GPU.

Abbiamo anche interrotto realmente un supervisore sintetico, dopo che il suo
worker era fermo ma prima del salvataggio dello stato finale. Riaprendo
l'archivio, il recupero conserva l'incompletezza. È una finestra di guasto
precisa, non una garanzia generale di recupero da qualunque crash.

La regressione conta 310 test passati: 246 documentali, 49 di cattura e
rendering, 15 dell'adattatore Local Trust. Sono test del software, non 310
documenti analizzati né una misura di efficacia contro gli attacchi.
Nessuna nuova percentuale di detection, nessun modello promosso, nessuna GPU
accesa per questi incrementi. Il prossimo passo è verificare il nuovo launcher
supervisionato sul modello reale.

Anche la ricerca ha un confine: raccogliere molti riferimenti non obbliga a
usarli. [README PageBench](https://www.kaggle.com/datasets/rodrigosfaria/passing-the-benchmark-pagebench)
merita approfondimento perché affronta il divario tra frasi e documenti;
altri dataset non entrano nel corpus se etichette, duplicazione o pertinenza
non reggono la verifica. La quantità non sostituisce la qualità dell'evidenza.

Il progetto resta sperimentale. Gli artefatti di laboratorio non sono tutti
pubblici: questa sintesi non costituisce un benchmark integralmente
riproducibile né una certificazione di sicurezza.
