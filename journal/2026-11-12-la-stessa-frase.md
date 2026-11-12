# La stessa frase, due decisioni

Oggi abbiamo seguito un falso negativo fino alla pagina del PDF. Il testo nativo era abbastanza lungo da saltare l’OCR, ma l’istruzione si trovava in un riquadro raster. Recuperata la regione e conservata la pagina, il giudice ha riconosciuto l’attacco. Un controllo con la stessa impaginazione e contenuto innocuo è passato.

Su altri tre documenti l’istruzione arrivava già integra. Il primo giudice la riconosceva in isolamento e la perdeva nel documento. Una seconda lettura con Mistral, prima locale e poi globale, ha recuperato i tre casi. Sulle coppie successive è emerso un limite più sottile: lo stesso comando può essere un ordine operativo oppure un esempio che un manuale invita a ignorare. Il modello sa citarlo e può comunque sbagliare il verdetto.

La prossima prova chiede due riscontri verificabili: dove compare il comando e quale altro passaggio dice al lettore come interpretarlo. Conserviamo anche le incertezze: una quarantena immotivata ha un costo reale per chi invia un documento legittimo.

Su quattro coppie di valutazione, Mistral ha classificato come injection tre dei quattro attacchi e uno dei quattro documenti benigni. Trattando anche gli incerti come motivo di quarantena, avremmo bloccato tutti e quattro gli attacchi e tre documenti benigni. Nel caso più istruttivo, il modello ha citato correttamente lo stesso comando in entrambi i PDF. Nella motivazione del falso allarme ha perfino riconosciuto che il manuale invitava a ignorare l’esempio.

Domani proveremo anche Wolf Defender Jev, un modello eseguibile su CPU che assegna probabilità a opzioni esplicite. Gli presenteremo lo stesso comando prima da solo e poi nella cornice di ciascun documento. Il testo estratto dei due PDF occupa rispettivamente 719 e 711 token con il suo tokenizer. Possiamo quindi conservare entrambe le pagine di ciascun documento e controllare il conteggio dell’intera richiesta. Il primo dato che cerchiamo è se il giudizio cambia quando cambia la funzione della frase. [Scheda di Wolf Defender Jev](https://huggingface.co/patronus-studio/wolf-defender-jev)
