#Appunti sulla Scalabilità dei Protocolli Blockchain (version 0.0.2)
Vitalik Buterin (Ethereum)

Commentatori e collaboratori: Gavin Wood, Vlad Zamfir (Ethereum),
Jeff Coleman (Kryptokit)
Marzo 14-31, 2015 Capitolo 1

##Introduzione

Allo stato attuale, tutti i protocolli di consenso blockchain, che vengono effettevamente utilizzati, hanno una limitazione critica: ogni nodo del network deve elaborare ogni transazione. Sebbene questo requisito assicuri un alto grado di sicurezza, rendendo del tutto impossibile ad un attaccante, che abbia perfino il controllo su ogni nodo che partecipa al processo di consenso, di convincere il network riguardo la validità di una transazione invalida, ciò richiede un costo molto alto: ogni protocollo blockchain che funziona in questo modo è obbligato a scegliere tra limitare sè stesso entro un tetto massimo di capacità di trasmissione, con il risultato di un costo alto per ciascuna transazione, e permettere un alto grado di centralizzazione.

Ciò che potrebbe forse sembrare una soluzione naturale, dividendo i dati pertinenti per mezzo di mille blockchains, comporta due fondamentali problemi consistenti nel  (i) decrescita della sciurezza, al punto che quella di ogni blockchain individuale mediamente non cresce affatti come l'utilizzo dell'ecosistema incremeta, e (ii) riduce massivamente il rischio di interoperatibilità. Altri approcci, come il merge-mining, sembrano avere una sicurezza più forte, anche se in pratica tali approcci sono nè

