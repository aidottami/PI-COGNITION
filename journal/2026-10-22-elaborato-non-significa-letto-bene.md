# Elaborato non significa letto bene

22 ottobre 2026 — prove CPU di estrazione, orientamento e recuperabilità.

Un documento può attraversare tutti i passaggi previsti e restituire comunque
una lettura sbagliata. È il risultato più importante delle ultime prove di
PIGUARD: la copertura del processo non certifica la qualità del testo estratto.

## Il successo silenzioso che non ci basta

Nel primo confronto, il percorso parallelo produceva gli stessi risultati del
seriale, più rapidamente. Anche sei interruzioni controllate confermavano il
recupero dei checkpoint pubblicati. Ma un campione con pagine raster ruotate
terminava con stato `complete` recuperando soltanto due marker su quattro e
122 riscontri testuali su 244.

Non era una misura di detection: i riscontri erano contenuti noti del campione,
usati per controllare la lettura. Eppure il problema riguarda direttamente la
sicurezza: ciò che non estraiamo non può essere valutato dai giudici successivi.

Abbiamo anche scoperto che due nomi del corpus non corrispondevano alle
difficoltà suggerite. Il controllo visivo e strutturale ha evitato una falsa
conferma. Per isolare il problema abbiamo aggiunto campioni con rotazione e
contrasto ridotto effettivamente verificati.

## Quattro orientamenti e un'incertezza esplicita

Il nuovo candidato CPU confronta letture a 0, 90, 180 e 270 gradi. Una prima
policy considera confidenza OCR, quantità di parole e differenza fra letture.
Quando il risultato è incerto conserva le alternative testuali distinte,
segna la pagina come parziale e continua sulle pagine successive. Il documento
resta `incomplete`, anche se tutti i passaggi sono stati eseguiti.

Sul campione ruotato il risultato passa a quattro marker su quattro e
238 riscontri su 244. Il miglioramento è concreto, ma sei riscontri restano
errati o mancanti: due pagine sono ancora segnalate come incerte.

Le soglie sono euristiche iniziali, non probabilità calibrate di correttezza.
Una confidenza alta può accompagnare una trascrizione sbagliata; le alternative
possono aggiungere rumore ai detector. La policy deve ancora essere calibrata
su un corpus più ampio, e non dimostra l'assenza di contenuti nascosti.

## Il costo di una pagina non è il suo numero

Quattro pagine A1 concorrenti avevano portato il picco di memoria vicino al
limite del worker. Il candidato governa ora la concorrenza in funzione dei
pixel da renderizzare, non soltanto del numero di pagine: budget di 24 milioni
di pixel concorrenti e limite di 20 milioni per pagina, a 150 dpi.

È una prima implementazione conservativa, basata sulla pagina più grande:
fino a otto worker per A4, uno per A1. Non è ancora uno scheduler dinamico
pesato pagina per pagina. Il budget del raster non limita da solo ogni costo
di decompressione o parsing; restano necessari i limiti del container.

| Prova | Risultato osservato |
|---|---|
| A1, quattro pagine | Picco da circa 910 a 303 MiB; tempo da circa 3 a 38 secondi |
| Raster ruotato, quattro pagine | 238/244 riscontri, 4/4 marker; circa 7 secondi, esito incompleto |
| Documento sparse, 44 pagine | Entrambi i marker distanti recuperati; circa 79 secondi, due pagine incerte |

L'aumento dei tempi comprende sia la riduzione della concorrenza sia i quattro
passaggi OCR: non possiamo attribuirlo a uno solo dei due interventi.
Sono singole misure per caso, non percentili di latenza o promesse di servizio.
I marker recuperati nel documento sparse non certificano il riconoscimento
di un attacco distribuito: verificano soltanto la presenza di quei frammenti.

## Verificare anche ciò che si interrompe

La versione finale è stata esercitata con nove estrazioni senza OOM o timeout
e quattro crash controllati: dopo la prima pagina e prima della finalizzazione,
sia sul documento ruotato sia sull'A1. Il recupero conserva i checkpoint già
pubblicati e restituisce un esito incompleto, senza promuovere il lavoro
parziale a un successo. I controlli successivi non hanno trovato processi orfani.

La suite da 401 test è passata su Mac e Linux. Un primo avvio del crash test
era fallito per due script mancanti nell'ambiente di prova: completato
l'ambiente, abbiamo ripetuto i test in directory nuove. Anche questa distinzione
conta: un errore dell'harness non è una prova di un difetto del worker.

Due controlli aggiuntivi verificano un foglio vuoto, trattato conservativamente
come lettura incerta, e una pagina oltre il budget, rifiutata prima del rendering
senza ridurre silenziosamente la risoluzione.

## Dove ci fermiamo, per ora

Tutto questo riguarda un candidato isolato, senza GPU e senza modifiche alla
produzione. Non è una nuova percentuale di efficacia contro la prompt injection.
Gli artefatti di laboratorio restano privati: queste sono sintesi sperimentali,
non un benchmark pubblico integralmente riproducibile.

I prossimi passi sono calibrare la qualità, indagare i riscontri ancora mancanti,
conservare score e orientamenti per pagina e ottimizzare il costo delle letture
multiple senza perdere copertura. Seguiranno prove di concorrenza tra più job.

La lezione resta semplice: terminare un'elaborazione e leggere bene un documento
sono due risultati diversi. Per costruire un giudizio affidabile dobbiamo
misurarli separatamente e rendere visibile ciò che ancora non sappiamo leggere.
