
# La filosofia di progettazione

Qui sotto abbiamo dettagliato i principi chiave che ispirano il lavoro sull'architettura e l'implementazione di Ethereum

## Semplicita'
Il protocollo di Ethereum dovrebbe essere il piu' semplice possible, persino a costo di qualche inefficienza nell'immagazzinamento dei dati o inefficienza temporale. Un programmatore qualunque dovrebbe essere idealmente in grado di seguire e implementare l'intero concetto, in modo anche da minimizzare l'influenza che ogni determinato individuo o un gruppo di elite puo' esercitare sul protocollo stesso e incoraggiare la visione che Ethereum e' un protocollo aperto a tutti. Le ottimizzazioni che aggiungono complessita' non dovrebbero essere apportate a meno che non portino benefici sostanziali. 

## Universalita' 
Una componente fondamentale della filosofia di progettazione di Ethereum e' che Ethereum non possiede "caratteristiche". Invece, Ethereum fornisce al suo interno un linguaggio di scripting Turing-completo che potete utilizzare per costruire qualsiasi "contratto intelligente" (smart contract) o tipologia di transazione che puo' essere definita matematicamente. Volete creare il vostro derivato finanziario? Con Ethereum, potete. Volete creare la vostra valuta? Fatelo con un contratto di Ethereum. Volete mettere in piedi un Daemon o Skynet in larga scala? Per farlo avrete forse bisogno di un paio di migliaia di contratti interellati, e dovrete far si' che si alimentino generosamente di carburante, ma niente vi proibisce di farlo.

## Modularita'
Componenti diverse del protocollo di Ethereum dovrebbero essere progettate in modo da poter essere il piu' modulari e separabili possibile. Durante la fase di sviluppo dovrebbe essere facile poter fare una piccola modifica sul protocollo e avere l'insieme dell'applicazione che continua a funzionare senza ulteriori modifiche. Quelle innovazioni come Dagger, i Patricia trees e RLP dovrebbero essere implementate come librerie separate e costruite per essere complete anche se Ethereum non ha bisogno di alcune determinate caratteristiche, ma in modo che possano essere utilizzabili anche per altri protoccoli. Lo sviluppo di Ethereum dovrebbe essere pensato per apportare beneficio a tutto l'ecosistema delle criptovalute, e non solo a se' stesso. 

## Non-Discriminazione 
Il protocollo non dovrebbe tentare di restringere attivamente o impedire determinate categorie di utilizzo, e i meccanismi di regolamentazione nel protocollo dovrebbero essere progettati in modo da regolare il danno, non da tentare di opporre specifiche applicazioni indesiderabili. Potete persino far girare uno script che genera un loop infinito su Ethereum fintanto che avete disponibilita' a pagare per la fee di transazione legata all'utilizzo computazionale della rete per farlo girare. 

## Agilita'
I dettagli del protocollo di Etheruem non sono marcati nella pietra. Nonostante il fatto che saremo estremamente giudiziosi nel fare modifiche ai costrutti di alto-livello come il linguaggio in stile C e il sistema di indirizzamento, prove computazionali piu' in avanti nel processo di sviluppo ci potranno indirizzare a scoprire che alcune modifiche all'algoritmo o al linguaggio di scripting miglioreranno sostanzialmente la scalabilita' o la sicurezza. Se avremo opportunita' del genere, state certi che ne faremo uso.
