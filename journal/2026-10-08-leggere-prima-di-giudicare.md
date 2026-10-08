# Leggere prima di giudicare

*8 ottobre 2026 — Il percorso, puntata 7*

Una pipeline contro la prompt injection deve prima riuscire a leggere ciò che sta analizzando. Un’istruzione presente in un’immagine, persa durante l’OCR o confusa nella ricostruzione di una tabella può non raggiungere mai i detector.

Per questo abbiamo dedicato una parte delle prove alla conversione dei documenti, distinguendola dall’analisi di sicurezza.

## Docling su CPU e GPU

Abbiamo confrontato Docling standard su CPU e NVIDIA A40 usando tre PDF di due pagine: testo su due colonne, una tabella multipagina e un documento con un’istruzione incorporata in un’immagine.

Due passaggi per documento e dispositivo: dodici conversioni riuscite. Il Markdown prodotto da CPU e GPU è risultato identico in tutti i confronti appaiati.

Nel secondo passaggio, con i modelli già caricati:

| Documento | CPU | GPU |
|---|---:|---:|
| Due colonne | 5,52 s | 4,96 s |
| Tabella multipagina | 11,07 s | 6,96 s |
| Istruzione in immagine | 6,79 s | 6,29 s |

Sono misure esplorative su un campione piccolo, non prestazioni generalizzabili. Comprendono la conversione, non soltanto l’OCR. Tesseract è rimasto su CPU.

## Velocità e fedeltà sono problemi diversi

La GPU ha aiutato soprattutto sulla tabella, ma non ha corretto una riga in cui il testo dell’originale si sovrapponeva alle altre celle.

L’istruzione nell’immagine è stata recuperata sia da Docling sia dal nostro percorso OCR precedente. Su questo caso, quindi, non abbiamo dimostrato una maggiore copertura grazie a Docling.

Abbiamo inoltre trovato aspettative strutturali del corpus incompatibili con i PDF effettivi. Anche il riferimento usato per valutare un estrattore deve essere verificato.

## Le prossime prove

La prima priorità sarà **Granite-Docling di IBM**, seguito da EasyOCR, Surya e dalla valutazione di RapidOCR, verificando versioni, provenienza dei pesi e licenze.

Questi strumenti riguardano la preparazione del documento: non sostituiscono i detector, il giudice contestuale o la politica decisionale.

Il criterio principale sarà la fedeltà: omissioni, alterazioni, aggiunte, ordine di lettura e conservazione delle istruzioni sparse. Misureremo anche tempi e consumi, ma estrarre più velocemente un documento incompleto non sarebbe un progresso.

Nessuno di questi risultati costituisce una validazione della detection o una promozione nella pipeline operativa.

## Aggiornamento serale dell’8 ottobre

Le prime prove con Granite-Docling ed EasyOCR sono ora concluse. Granite ha recuperato l’istruzione nell’immagine, ma sulla tabella è entrato in ripetizione alla riga 17 fino a esaurire gli 8.192 token disponibili. L’output generativo non chiudeva la tabella; l’esportazione la restituiva vuota pur dichiarando SUCCESS. La diagnosi distingue quindi un problema di generazione da una segnalazione insufficiente dell’incompletezza. Il tentativo mirato per regioni non ha fornito un recupero affidabile: sono emerse celle aggiunte e un’altra ripetizione. Risoluzione e percorso di conversione differivano, quindi non ne ricaviamo una conclusione generale sui metodi per regioni.

EasyOCR su GPU è stato confrontato con Tesseract su CPU su sei pagine renderizzate a 300 DPI. Entrambi recuperano il marker nell’immagine; in questa prova, con una sola misura per pagina e senza ottimizzazione, EasyOCR non mostra un vantaggio di velocità né nei controlli limitati sugli identificatori. Non è una graduatoria generale fra OCR.

Manteniamo pertanto il percorso nativo più Tesseract come riferimento. Un controllo sperimentale riconosce limite generativo, ripetizioni e strutture non chiuse: sette test unitari e il replay del fallimento reale passano. L’integrazione nel runner resta da validare, anche su output corretti. Nessun candidato è stato promosso: la priorità è impedire che un’estrazione incompleta venga scambiata per copertura verificata.
