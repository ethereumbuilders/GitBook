#Appunti sulla Scalabilità dei Protocolli Blockchain (version 0.0.2)
Vitalik Buterin (Ethereum)

Commentatori e collaboratori: Gavin Wood, Vlad Zamfir (Ethereum),
Jeff Coleman (Kryptokit)
Marzo 14-31, 2015 Capitolo 1

##Introduzione

Allo stato attuale, tutti i protocolli di consenso blockchain, che vengono effettevamente utilizzati, hanno una limitazione critica: ogni nodo del network deve elaborare ogni transazione. Sebbene questo requisito assicuri un alto grado di sicurezza, rendendo del tutto impossibile ad un attaccante, che abbia perfino il controllo su ogni nodo che partecipa al processo di consenso, di convincere il network riguardo la validità di una transazione invalida, ciò richiede un costo molto alto: ogni protocollo blockchain che funziona in questo modo è obbligato a scegliere tra limitare sè stesso entro un tetto massimo di capacità di trasmissione, con il risultato di un costo alto per ciascuna transazione, e permettere un alto grado di centralizzazione.

Ciò che potrebbe forse sembrare una soluzione naturale, dividendo i dati pertinenti per mezzo di mille blockchains, comporta due fondamentali problemi consistenti nel  (i) decrescita della sciurezza, al punto che quella di ogni blockchain individuale mediamente non cresce affatti come l'utilizzo dell'ecosistema incremeta, e (ii) riduce massivamente il rischio di interoperatibilità. Altri approcci, come il merge-mining, sembrano avere una sicurezza più forte, anche se in pratica tali approcci sono nè




# PARTE DI MASSI
* Il protocollo potrebbe aggiungere facoltativamente una fee obbligatoria alle transazioni e ai blocchi, e tale fee puo’ essere distrutta. Il protocollo puo’ anche aggiungere una ricompensa “ex nihilo” per i validatori dei super-blocchi, invece di richiedere che questa sia pagata dalle fee di transazione. 

Possiamo anche richiedere ai validatori di mantenere depositi di sicurezza di dimensione O (N 1-) che vengono distrutti se i validatori sono abusivi, come e’ standard nei moderni schemi di consenso basati su una proof-of-stake; cio’ garantisce le basi per un margine di sicurezza economico. Notate che le fee pagate ai validatori, allo stesso modo della dimensione del deposito di un validatore – sono contenute nella catena di intestazioni insieme all’identita’ del validatore; solo quando un validatore lascia il pool il bilancio delle fee viene aggiunto allo stato principale. 

## 20 Capitolo 6

### Fallback Schemes

L’algoritmo precedente fornisce una garanzia statistica della disponibilita’ e della validita’ dei  dati semplicemente ipotizzando che almeno due terzi dei validatori siano onesti; nonostante cio’, soffre di una fragilita’ intrinseca: se, per qualche motivo, un piccolo campione di nodi finisce per confermare un blocco non valido, a quel punto il sistema dovra’ “HARD FORK” quando verra’ scoperto il problema. Cio’ da’ luogo a due debolezze pratiche. In primo luogo, fattori di sicurezza piu’ grandi sono necessari in merito alla dimensione del campione in modo tale da assicurare che un campione sia – in maniera assoluta – sempre genuino. In secondo luogo, la sicurezza economica dell’algoritmo, secondo la nostra formale definizione, si avvicina a zero fintanto che L aumenta se definita come una frazione di L. 

Un hacker dovra’ solamente corrompere m su un totale di O(N 1- ) validatori per produrre un blocco non valido che si inserisce nel sistema. Percio’, fintanto che N cresce, non c’e’ un limite fisso b di proporzionalita’ tra il peso e il margine di sicurezza; il rapporto decresce all’allargarsi dal sistema. Il campionamento da’ sicurezza contro le falle casuali, ma non contro le falle provocate, che potrebbero teoricamente avvenire mediante la corruzione.
