Solidity e' un linguaggio di alto livello la cui sintassi e' simile a quella di JavaScript ed e' stato progettatto per compilare codice per la Virtual Machine di Ethereum. Questo tutorial fornisce una introduzione di base a Solidity e assume che abbiate una qualche familiarita' con la Virtual Machine di Ethereum e - in generale - la programmazione. Per ulteriori dettagli, riferitevi alla specificazione formale di Solidity (che non e' ancora stata scritta). Questo tutorial non comprende le caratteristiche della documentazione naturale del linguaggio o la verifica formale e neanche e' pensato per essere una specificazione finale del linguaggio. 

Potete cominciare con utilizzare [Solidity nel vostro browser](http://chriseth.github.io/cpp-ethereum),
senza aver bisogno di scaricare o di compilare alcunche'. Questa applicazione supporta unicamente la compilazione - se volete eseguire il codice e metterlo sulla blockchain, dovete utilizzare un client come ad esempio Alethzero. 

## Semplice esempio

```
contract SimpleStorage {
    uint storedData;
    function set(uint x) {
        storedData = x;
    }
    function get() constant returns (uint retVal) {
        return storedData;
    }
}
```

`uint storedData` dichiara una variabile di stato chiamata `storedData` di tipo `uint`
(numero intero non firmato, "unsigned integer"  di 256 bits) la cui posizione nello storage e' automaticamente allocata dal compilatore. Le funzioni `set` e `get` possono essere utilizzate per recuperare il valore della variabile.

## Esempio di sottovaluta

```
contract Coin {
    address minter;
    mapping (address => uint) balances;
    function Coin() {
        minter = msg.sender;
    }
    function mint(address owner, uint amount) {
        if (msg.sender != minter) return;
        balances[owner] += amount;
    }
    function send(address receiver, uint amount) {
        if (balances[msg.sender] < amount) return;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
    }
    function queryBalance(address addr) constant returns (uint balance) {
        return balances[addr];
    }
}
```

Questo contratto presenta dei nuovi concetti. Uno di questi e' il tipo `address`, che e' un valore di 160 bit che non consente di compiere alcuna operazione aritmetica.
Inoltre, la variable di stato `balance` è un datatype complesso che mappa indirizzi per interi senza segno. I mappings possono essere visti come tavole di hash che sono praticamente inizializzate in modo che esista ogni chiave possibile ed e' associata ad un valora la cui rappresentazione in byte è di tutti zeri. La funzione speciale `Coin` è il costruttore che viene eseguito durante la creazione del contratto e non può essere eseguito in seguito. Memorizza permanentemente l'indirizzo della persona che crea il contratto: Insieme a `tx` e `block`, `msg` è una variabile magica globale che possiede alcune proprietà che consentono l'accesso al mondo esterno al contratto.
La funzione `queryBalance` è dichiarata come `constant` e quindi non è consentito modificare lo stato del contratto (si noti però che cio' non è ancora implementato). Su Solidity, i parametri "return" vengono chiamati ed essenzialmente creano una variabile locale. Quindi per restituire il saldo, potremmo anche semplicemente usare `balance = balances[addr];` senza alcuna istruzione di tipo return.

## I Commenti

E' possibile fare commenti a linea singola (`//`) e a linea multipla (`/*...*/`), mentre i commenti con tre barre (`///`) prima della dichiarazione di una funzione introducono i commenti NatSpec (che non sono descritti qui).

## I tipi di dati

I tipi (elementari) di dati implementati al momento sono booleani (`bool`), interi e array di stringhe/byte a lunghezza fissa (da bytes0 a bytes32).
I tipi interi sono sia signed e unsigned di varie dimensioni di bits (da `int8`/`uint8` a `int256`/`uint256` in sequenze di 8 bits, laddove `uint`/`int` definiscono i tipi `uint256`/`int256`) e gli indirizzi (di 160 bits).

Le funzioni di comparazione (`<=`, `!=`, `==`, etc.) danno sempre come risultato dei tipi booleani che possono essere combinati utilizzando `&&`, `||` e `!`. Notate che le scorciatoie comuni si applicano per  `&&` e `||`, che significa che per le espressioni in forma `(0 < 1 || fun())`, la funzione non viene mai eseguita.

Se un operatore e' applicato a un tipo diverso, il compilatore prova a convertire implicitamente uno degli operandi agli tipo dell'altro (e' vero lo stesso per gli assignments). In generale, una conversione implicita e' possibile se ha senso semanticamente e nessuna informazione viene perduta: `uint8` e' convertibile in `uint16` e `int120` in `int256`, ma `int8` non e' convertibile in `uint256`.
Inoltre, interi unsigned e signed possono essere convertiti a bytes di uguale o di piu' grande dimensione, ma non viceversa. Ogni tipo puo' essere convertito a `uint160` ma puo' anche essere convertito in `address`.

Se il compilatore non consente una conversione implicita ma sapete cosa state facendo, talvolta e' anche possibile una conversine esplicita:

```
int8 y = -3;
uint x = uint(y);
```

Alla fine di questo pezzo di codice, `x` avra' il valore `0xfffff..fd` (64 caratteri esadecimali), che rappresenta -3 i due complementari rappresentazioni di 256 bits.

Per convenienza non e' sempre necessario specificare esplicitamente il tipo di una variabile, il compilatore infatti lo inferisce automaticamente dal tipo di prima operazione che e' stata assegnata ad una variabile:
```
uint20 x = 0x123;
var y = x;
```
Qui il tipo di `y` sara' `uint20`. Utilizzando `var` non e' possibile per i parametri funzione e return.
Le variabili di stato integer e bytesXX possono essere dichiarate come costanti.

```
uint constant x = 32;
bytes3 constant text = "abc";
```

## I numeri interi letterali

Il tipo di interi letterali non e' determinato fintanto che gli interi letterali sono mescolati tra loro. Probabilmente e' piu' semplice da spiegare con degli esempi: 
```
var x = 1 - 2;
```
Il valore di `1 - 2` e' `-1`, che e' assegnato a  `x` e quindi `x` riceve il tipo `int8` -- il tipo piu' piccolo che contiene `-1`. Nononostante cio', il codice seguente si comporta in modo diverso: 
```
var uno = 1;
var due = 2;
var x = uno - due;
```

In questo caso `uno` and `due` sono entrambi di tipo `uint8` che si propaga anche a `x`. La sottrazione dentro un tipo `uint8` causa un arrovellamento e quindi il valore della `x` sara' di `255` (ricordate che il tipo `uint` non contiene numeri negativi).

E' persino possibile eccedere temporaneamente il massimo di 256 bits fintanto che solamente gli interi letterali sono utilizzati per il calcolo:
```
var x = (0xffffffffffffffffffff * 0xffffffffffffffffffff) * 0;
```
Qui `x` prendera' il valore `0` e quindi il tipo `uint8`.

## Ether e le Unita' Temporali

Un numero letterale puo' assumere il suffisso `wei`, `finney`, `szabo` o `ether` per essere convertito tra le denominazioni dell'ether, laddove si assume che i valori in Ether privi di suffisso siano "wei", ad esempio `2 ether == 2000 finney` e' `true`.

Inoltre, i suffissi `seconds`, `minutes`, `hours`, `days`, `weeks` e `years` possono essere adoperati per convertire tra unita' temporali, laddove i secondi sono l'unita' di base e le unita' sono convertite in maniera semplice (i.e. un anno e' sempre esattamente 365 giorni, ecc.).

## Le Strutture di Controllo

La maggior parte delle strutture di controllo di C/Javascript sono disponibili su Solidity tranne che per `switch` (supporto non pianificato) e `goto` (che e' invece chiamato Solidity). Dunque avete: `if`, `else`, `while`, `for`, `break`, `continue`, `return`. Notate che non e' possibile una conversione di tipo tra i non-booleani e i booleani come in C and JavaScript, quindi `if (1) { ... }` _non_ e' valido su Solidity.

## Richiamare le funzioni

Le funzioni del contratto corrente possono essere richiamate direttamente, anche in maniera ricorsiva, come potete vedere in questo esempio senza senso:

```
contract c {
  function g(uint a) returns (uint ret) { return f(); }
  function f() returns (uint ret) { return g(7) + f(); }
}
```

Anche l'espressione `this.g(8);` e' una chiamata di funzione valida, ma questa volta la funzione verra richiamata tramite una chiamata di messaggio e non direttamente via salti. Quando richiamate funzioni di altri contratti, potete specificare l'ammontare di Wei inviato con la chiamata e il gas: 
```
contract InfoFeed {
  function info() returns (uint ret) { return 42; }
}
contract Consumer {
  InfoFeed feed;
  function setFeed(address addr) { feed = InfoFeed(addr); }
  function callFeed() { feed.info.value(10).gas(800)(); }
}
```
Notate che l'espressione `InfoFeed(addr)` compie una conversione esplicita di tipo, dicendo che  "sappiamo che il tipo del contratto a quel determinato address e' `InfoFeed` e quindi questo non esegue il construttore. Sate attenti perche' `feed.info.value(10).gas(800)` setta solo (localmente) il valore e l'ammontare di gas inviati con la funzione chamata e solo le parentesi alla fine `()` compiono la chiamata vera e propria. 

Gli argomenti delle funzioni di chiamata possono anche essere dati per nome, in qualsiasi ordine:
```
contract c {
function f(uint key, uint value) { ... }
function g() {
  f({value: 2, key: 3});
}
}
```
I name dei parametri delle funzioni e dei parametri return sono opzionali. 
```
contract test {
  function func(uint k, uint) returns(uint){
    return k;
  }
}
```

## Variabili e Funzioni speciali

Esistono variabili e funzioni speciali che esistono globalmente nel codice dei contratti. 

### Proprieta' dei Blocchi e delle Transazioni

 - `block.coinbase` (`address`): l'indirizzo del miner del blocco attuale
 - `block.difficulty` (`uint`): la difficolta' del blocco attuale
 - `block.gaslimit` (`uint`): il gaslimit del blocco attuale
 - `block.number` (`uint`): il numero del blocco attuale
 - `block.blockhash` (`function(uint) returns (bytes32)`): l'hash del dato blocco
 - `block.timestamp` (`uint`): il timestamp del blocco attuale
 - `msg.data` (`bytes`): completa il calldata
 - `msg.gas` (`uint`): gas rimanente
 - `msg.sender` (`address`): mittente del messaggio (chiamata corrente)
 - `msg.value` (`uint`): ammontare di wei spedito con il messaggio
 - `now` (`uint`): il timestamp del blocco attuale (alias for `block.timestamp`)
 - `tx.gasprice` (`uint`): prezzo in gas della transazione
 - `tx.origin` (`address`): mittente della tranzione (chiamata completa alla chain)

### Funzioni Crittografiche

 - `sha3(...) returns (bytes32)`: calcola l'hash SHA3 dell'argomento (tightly packed)
 - `sha256(...) returns (bytes32)`: calcola l'hash SHA256 dell'argomento (tightly packed)
 - `ripemd160(...) returns (bytes20)`: calcola il RIPEMD di 256 dell'argomento (tightly packed)
 - `ecrecover(bytes32, byte, bytes32, bytes32) returns (address)`: recupera la chiave pubblica dalla firma a curva ellittica

Nel codice sopra, "tightly packed" significa che gli argomenti sono concatenati senza imbottitura, i.e.
`sha3("ab", "c") == sha3("abc") == sha3(0x616263) == sha3(6382179) = sha3(97, 98, 99)`. Se c'e' bisogno dell'imbottitura, e' richiesta una conversione esplicita di tipo. 

### Relative ai contratti

 - `this` (tipo dell'attuale contratto type): il contratto attuale, convertibile esplicitamente in `address`
 - `suicide(address)`: fa "suicidare" il contratto attuale, inviando i suoi fondi al dato indirizzo

Inoltre, tutte le funzioni del contratto corrente sono richiamabili direttamente includendo la funzione corrente. 

## Funzioni sugli indirizzi

E' possibile chiedere il saldo di un indirizzo usando la proprieta' `balance` ed inviare Ether (in unita' Wei) ad un indirizzo utilizzando la funzione  `send`:

```
address x = 0x123;
if (x.balance < 10 && address(this).balance >= 10) x.send(10);
```

Inoltre, per interfacciarsi con i contratti che non aderiscono all'ABI (come il classico contratto NameReg),
e' fornita la funzione `call` che prende un numero casuale di argomenti di qualsiasi tipo. Questi argomenti sono serializzati in ABI (i.e. imbottiti anch'essi a  32 bytes). Una eccezione e' quel caso in cui il primo argomento e' codificato in esattamente quattro bytes. In questo caso, non e' imbottito per permettere l'uso della funzione firma.

```
address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
nameReg.call("register", "MyName");
nameReg.call(bytes4(sha3("fun(uint256)")), a);
```

Notate che i contratti ereditano tutti i membri dell'indrizzo, quindi e' possibile chiedere il saldo del contratto corrente utilizzando `this.balance`.

## Ordine di esecuzione delle espressioni

L'ordine di esecuzione delle espressioni non e' specificato (piu' formalmente, l'ordine nel quale i discendenti di un nodo nell'albero dell'espressione sono valutati non e' specificato, ma certamente sono valutit prima dello stesso nodo). Si ha solo la garanzia che le linee di codice sono eseguite in ordine e' possibile la cortocircuitazione mediante espressioni booleane.

## Gli Arrays

Gli arrays di dimensione variable e fissa sono supportati nella memorizzazione e come parametri di funzioni esterne:

```
contract ArrayContract {
  uint[2**20] m_aLotOfIntegers;
  bool[2][] m_pairsOfFlags;
  function setAllFlagPairs(bool[2][] newPairs) {
    // l'assegnamento all'array sostituisce l'array completo
    m_pairsOfFlags = newPairs;
  }
  function setFlagPair(uint index, bool flagA, bool flagB) {
    // accedendo ad un indice non esistente verra' bloccata l'esecuzione
    m_pairsOfFlags[index][0] = flagA;
    m_pairsOfFlags[index][1] = flagB;
  }
  function changeFlagArraySize(uint newSize) {
    // se la nuova dimensione e' inferiore, gli elementi dell'array rimossi verranno cancellati
    m_pairsOfFlags.length = newSize;
  }
  function clear() {
    // questi cancellano completamente l'array
    delete m_pairsOfFlags;
    delete m_aLotOfIntegers;
    // effetto identico qui
    m_pairsOfFlags.length = 0;
  }
  bytes m_byteData;
  function byteArrays(bytes data) external {
    // i byte arrays ("bytes") cambiano quando vengono messi in memoria senza il padding,
    // ma possono essere trattati esattamente come "uint8[]"
    m_byteData = data;
    m_byteData.length += 7;
    m_byteData[3] = 8;
    delete m_byteData[2];
  }
}
```

## Gli Structs

Solidity fornisce un modo di definire un nuovo tipo nella forma di uno `struct`, che e' mostrato nell'esempio precedente: 

```
contract CrowdFunding {
  struct Funder {
    address addr;
    uint amount;
  }
  struct Campaign {
    address beneficiary;
    uint fundingGoal;
    uint numFunders;
    uint amount;
    mapping (uint => Funder) funders;
  }
  uint numCampaigns;
  mapping (uint => Campaign) campaigns;
  function newCampaign(address beneficiary, uint goal) returns (uint campaignID) {
    campaignID = numCampaigns++; // campaignID e' la variabile restituita
    Campaign c = campaigns[campaignID];  // assegna una reference
    c.beneficiary = beneficiary;
    c.fundingGoal = goal;
  }
  function contribute(uint campaignID) {
    Campaign c = campaigns[campaignID];
    Funder f = c.funders[c.numFunders++];
    f.addr = msg.sender;
    f.amount = msg.value;
    c.amount += f.amount;
  }
  function checkGoalReached(uint campaignID) returns (bool reached) {
    Campaign c = campaigns[campaignID];
    if (c.amount < c.fundingGoal)
      return false;
    c.beneficiary.send(c.amount);
    c.amount = 0;
    return true;
  }
}
```

Questo contratto non fornisce tutte le funzioni di un contratto tipico di crowdfunding, ma contiene i concetti di base necessari a capire gli `structs`. 
I tipi struct possono essere usati come tipi di valore per i mappings e possono loro stessi contenere dei mappings (anche se gli stessi struct possono essere il tipo di valore per i mapping, nonostante non sia possibile includere uno struct dentro un altro struct). Notate che in tutte le funzioni, un tipo struct e' assegnato ad una variabile locale. Questo non copia lo struct, ma immagazzina solamente una reference, cosicche' gli assegnamenti ai membri della variabile locale scrivono effettivamente allo stato.

## Enums

Gli Enums costituiscono un'altra soluzione per creare un tipo user-defined in Solidity. Sono convertibili in maniera esplicita in e da qualsiasi tipo integer, mentre la conversione implicita non è ammessa. E' invece possibile dichiarare una variabile di tipo enum come costante.

```
contract test {
    enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill }
    ActionChoices choices;
    ActionChoices constant defaultChoice = ActionChoices.GoStraight;
    function setGoStraight()
    {
        choices = ActionChoices.GoStraight;
    }
    function getChoice() returns (uint)
    {
        return uint(choices);
    }
    function getDefaultChoice() returns (uint)
    {
        return uint(defaultChoice);
    }
}
```

## Interfacciamento con gli altri Contratti

Esistono due modi per interfacciarsi con altri contratti: richiamare un metodo di un contratto il cui indirizzo e' conosciuto, oppure creare un nuovo contratto. Entrambi gli usi sono mostrati nell'esempio che segue. Si noti che (ovviamente) il codice sorgente da creare deve essere noto; in altre parole, esso deve precedere il contratto che lo crea (e le dipendenze cicliche non sono possibili, perche' il bytecode del nuovo contratto e' contenuto nel bytecode del contratto che lo genera).

```
contract OwnedToken {
  // TokenCreator è un tipo di contratto che viene qui sotto definito. E' possibile citarlo
  // fintanto che ess non sara' usato per creare un nuovo contratto.
  TokenCreator creator;
  address owner;
  bytes32 name;
  function OwnedToken(bytes32 _name) {
    address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
    nameReg.call("register", _name);
    owner = msg.sender;
    // Facciamo una conversione esplicita del tipo (un cd. "cast") da `address` to `TokenCreator`
    // e assumiamo che il tipo del contratto chiamante sia TokenCreator, sebbene non vi sia un modo 
    // per controllare cio'.
    creator = TokenCreator(msg.sender);
    name = _name;
  }
  function changeName(bytes32 newName) {
    // Solo il creatore ha la facolta' di alterare il nome -- i contratti sono esplicitamente 
    // convertibili in indirizzi.
    if (msg.sender == address(creator)) name = newName;
  }
  function transfer(address newOwner) {
    // Solo il proprietario corrente puo' trasferire il token.
    if (msg.sender != owner) return;
    // Vogliamo poi chiedere al contratto generatore se il trasferimento è stato completato con successo.
    // Si noti che questo chiama una funzione del contratto sotto definita.
    // Se la chiamata fallisce (ad es. a causa della mancanza di gas), l'esecuzione si blocca immediatamente
    // in questo punto (la possibilita' di cogliere cio' verra' aggiunta successivamente).
    if (creator.isTokenTransferOK(owner, newOwner))
      owner = newOwner;
  }
}
contract TokenCreator {
  function createToken(bytes32 name) returns (address tokenAddress) {
    // Crea un nuovo Token contract e restituisce il suo indirizzo.
    return address(new OwnedToken(name));
  }
  function changeName(address tokenAddress, bytes32 name) {
    // Abbiamo bisogno di una conversione esplicita del tipo in quanto i tipi contrattuali non fanno parte dell'ABI.
    OwnedToken token = OwnedToken(tokenAddress);
    token.changeName(name);
  }
  function isTokenTransferOK(address currentOwner, address newOwner) returns (bool ok) {
    // Controlla alcune condizioni arbitrarie.
    address tokenAddress = msg.sender;
    return (sha3(newOwner) & 0xff) == (bytes20(tokenAddress) & 0xff);
  }
}
```

## Argomenti dei Costruttori

Un contratto Solidity richiede che gli argomenti del costruttore siano specificati dopo i dati del contratto stesso.
Questo implica che il passaggio ad un contratto degli argomenti venga perfezionato inserendoli dopo i bytes compilati, nel momento in cui il compilatore li restituisce nel solito formato ABI.

## Ereditarieta' dei Contratti

Solidity supporta l'ereditarieta' multipla tramite copiatura del codice, polimorfismo incluso.
Nell'esempio che segue forniamo i dettagli.

```
contract owned {
    function owned() { owner = msg.sender; }
    address owner;
}

// Va usato "is" per derivare da un altro contratto. I contratti derivati possono accedere a tutti i membri
// incluse le funzioni private e le variabili in memoria.
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}

// Stiamo fornendo questi contratti con il solo obiettivo di rendere nota l'interfaccia al compilatore. 
contract Config { function lookup(uint id) returns (address adr) {} }
contract NameReg { function register(bytes32 name) {} function unregister() {} }

// L'ereditarieta' multipla e' possibile. Si noti che "owned" e' anche la classe di base di
// "mortal", sebbene vi sia soltanto una singola istanza di "owned" (concetto mutuato dall'ereditarieta'
// virtuale in C++).
contract named is owned, mortal {
    function named(bytes32 name) {
        address ConfigAddress = 0xd5f9d8d94886e70b06e474c3fb14fd43e2f23970;
        NameReg(Config(ConfigAddress).lookup(1)).register(name);
    }

// Le funzioni possono essere sovrascritte. Sia le chiamate di funzioni locali che message-based tengono
// conto di queste sovrascritture.
    function kill() {
        if (msg.sender == owner) {
            address ConfigAddress = 0xd5f9d8d94886e70b06e474c3fb14fd43e2f23970;
            NameReg(Config(ConfigAddress).lookup(1)).unregister();
// E' comunque ancora possibile richiamare una specifica funzione sovrascritta. 
            mortal.kill();
        }
    }
}

// Se un costruttore prende un argomento, esso (l'argomento) va fornito nell'header.
contract PriceFeed is owned, mortal, named("GoldFeed") {
   function updateInfo(uint newInfo) {
      if (msg.sender == owner) info = newInfo;
   }

   function get() constant returns(uint r) { return info; }

   uint info;
}
```

Si badi che nell'esempio appena mostrato, abbiamo ordinato a `mortal.kill()` di "forwardare" la richiesta di distruzione. Il modo in cui questa richiesta viene inviata è, come si può notare nell'esempio che segue, alquanto problematica:
```
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}
contract Base1 is mortal {
    function kill() { /* do cleanup 1 */ mortal.kill(); }
}
contract Base2 is mortal {
    function kill() { /* do cleanup 2 */ mortal.kill(); }
}
contract Final is Base1, Base2 {
}
```

Il comando di esecuzione di `Final.kill()` richiama a sua volta `Base2.kill` poiche' l'oggetto piu' derivato sovrascrive l'antenato, ma questa funzione bypassera' `Base1.kill`, in quanto essa ignora persino `Base1`.
Il metodo inverso consiste nell'usare `super`:
```
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}
contract Base1 is mortal {
    function kill() { /* do cleanup 1 */ super.kill(); }
}
contract Base2 is mortal {
    function kill() { /* do cleanup 2 */ super.kill(); }
}
contract Final is Base1, Base2 {
}
```

Se `Base1` richiama una funzione di `super`, esso, piuttosto che comandare semplicemente l'esecuzione di tale funzione in uno dei suoi contratti di base, la fa eseguire sul contratto di base successivo all'interno del grafo di ereditarieta' finale, per cui richiamera' `Base2.kill()`. Va notato che la funzione realmente richiamata quando si usa il costrutto super non e' nota nel contesto della classe in cui esso e' usato, mentre e' noto il suo tipo. Questo vale anche per il metodo ordinario virtuale lookup.

### Ereditarieta' multipla e Linearizzazione

I linguaggi che ammettono ereditarieta' multipla debbono confrontarsi con molti problemi, tra cui il [Problema del Diamante](https://it.wikipedia.org/wiki/Ereditariet%C3%A0_multipla#Problema_del_diamante). Solidity seguendo la via scelta dagli sviluppatori di Python usa la "[C3 Linearization](https://en.wikipedia.org/wiki/C3_linearization)" per forzare un ordine specifico nella [DAG](https://github.com/ethereum/wiki/wiki/Ethash-DAG) delle classi di base. Da ciò deriva una comoda proprieta' di monotonicita' ma rende inammissibili alcuni grafi di ereditarieta'. In particolare, e' fondamentale l'ordine in cui le classi di base sono fornite nella direttivais`. In un codice come quello che segue, Solidity restituisce un errore "Linearization of inheritance graph impossible".
```
contract X {}
contract A is X {}
contract C is A, X {}
```
Il motivo e' che `C` richiede a `X` di sovrascrivere `A` (specificando `A, X` in quest'ordine), ma la stessa `A` richiede di sovrascrivere `X`, scatenando una contraddizione irrisolvibile.

Una semplice regola da ricordare e' di specificare le classi di base in ordine di derivazione, dalla piu' basica alla piu' derivata.

## Contratti astratti

Alle funzioni Contratto possono mancare le implementazioni come nell'esempio che segue (notare che l'header della dichiarazione della funzione viene terminato da `;`).
```
contract feline {
  function utterance() returns (bytes32);
}
```
Questo tipo di contratti non possono essere compilati (sebbene contengano, assieme alle funzioni non implementate, anche funzioni implementate), ma possono comunque essere utilizzate come contratti di base:
```
contract Cat is feline {
  function utterance() returns (bytes32) { return "miaow"; }
}
```
Se un contratto eredita da un contratto astratto e non implementa tutte le funzioni non implementate sovrascrivendole, esso stesso sara' astratto.

## Specificatori di visibilita'

Le funzioni e le variabili di memoria possono essere specificate come `public`, `internal` or `private`, (l'impostazione di default e' `public` per le funzioni e `internal` per le variabili di memoria). In aggiunta, le funzioni possono anche essere specificate come `external`.

External: le funzioni esterne sono parte dell'interfaccia di un contratto, e possono essere richiamate da altri contratti per mezzo delle transazioni. Una funzione esterna `f` non puo' essere richiamata internamente (cioè `f()` non funziona, mentre `this.f()` si'). Inoltre, tutti i parametri delle funzioni sono immutabili.

Public: le funzioni pubbliche sono parte delle interfacce dei contratti e possono essere richiamate sia internamente che tramite messaggi. Per le variabili di memoria, viene generata automaticamente una funzione di accesso (vedi sotto).

Inherited: a tali funzioni e variabili di memoria si puo' accedere soltanto internamente.

Private: le funzioni e le variabili di memoria private sono visibili solamente per il contratto in cui esse sono definite, e non all'interno di contratti derivati.

```
contract c {
  function f(uint a) private returns (uint b) { return a + 1; }
  function setData(uint a) inherited { data = a; }
  uint public data;
}
```

Altri contratti possono richiamare `c.data()` per accedere ai valori dei dati in memoria, ma non sono in grado di far eseguire `f`. I contratti derivati da `c` possono richiamare `setData` per alterare i valori di `data` (ma solo all'interno del loro spazio di memoria).

## Funzioni di accesso

Il compilatore automaticamente crea funzioni per tutte le variabili di stato pubbliche. Il contratto che viene mostrato di seguito, avra' una funzione chiamata `data`, priva di argomenti, e che restituira' un uint, il valore della variabile di stato `data`. L'inizializzazione delle variabili di stato puo' essere effettuata in fase di dichiarazione.

```
contract test {
     uint public data = 42;
}
```

L'esempio che segue e' leggermente piu' complesso:

```
contract complex {
  struct Data { uint a; bytes3 b; mapping(uint => uint) map; }
  mapping(uint => mapping(bool => Data[])) public data;
}
```

Generera' una funzione di forma...
```
function data(uint arg1, bool arg2, uint arg3) returns (uint a, bytes3 b)
{
  a = data[arg1][arg2][arg3].a;
  b = data[arg1][arg2][arg3].b;
}
```

Va sottolineato che la mappatura nello struct viene omessz perche' non esiste un metodo efficace di fornire una chiave per la mappatura.

## Funzioni di fallback (ripiego)

Un contratto puo' avere esattamente una funzione anonima (senza nome). Tale funzione non puo' possedere argomenti e viene eseguita su richiesta di esecuzione del contratto se nessuna altra funzione e' compatibile con l'identificatore della funzione dato (o se non vengono affatto forniti dati).

```
contract Test {
  function() { x = 1; }
  uint x;
}

contract Caller {
  function callTest(address testAddress) {
    Test(testAddress).send(0);
    // restituisce Test(testAddress).x che diventa == 1.
  }
}
```

## Modificatori di funzione

I modificatori possono essere utilizzati per cambiare facilmente il comportamento delle funzioni, ad esempio per il controllo automatico di una condizione che precede l'esecuzione della funzione. Sono proprieta' ereditabili dei contratti e possono essere sovrascritti da contratti derivati.

```
contract owned {
  function owned() { owner = msg.sender; }
  address owner;

  // Questo contratto definisce un modificatore ma non lo usa - esso verra'
  // sfruttato in contratti derivari.
  // Il corpo della funzione e' inserito nel punto in cui appare il simbolo speciale "_"
  // nella definizione di un modificatore.
  modifier onlyowner { if (msg.sender == owner) _ }
}
contract mortal is owned {
  // QUesto contratto eredita il modificatore "onlyowner" da "owned" e
  // lo applica alla funzione "kill". L'effetto finale e' che i richiami all'esecuzione di "kill"
  // hanno effetto solo se fatti dallo stored owner.
  function kill() onlyowner {
    suicide(owner);
  }
}
contract priced {
  // I modificatori possono ricevere argomenti:
  modifier costs(uint price) { if (msg.value >= price) _ }
}
contract Register is priced, owned {
  mapping (address => bool) registeredAddresses;
  uint price;
  function Register(uint initialPrice) { price = initialPrice; }
  function register() costs(price) {
    registeredAddresses[msg.sender] = true;
  }
  function changePrice(uint _price) onlyowner {
    price = _price;
  }
}
```

Multiple modifiers can be applied to a function by specifying them in a whitespace-separated list and will be evaluated in order. Explicit returns from a modifier or function body immediately leave the whole function, while control flow reaching the end of a function or modifier body continues after the "_" in the preceding modifier. Arbitrary expressions are allowed for modifier arguments and in this context, all symbols visible from the function are visible in the modifier. Symbols introduced in the modifier are not visible in the function (as they might change by overriding).

## Events

Events allow the convenient usage of the EVM logging facilities. Events are inheritable members of contracts. When they are called, they cause the arguments to be stored in the transaction's log. Up to three parameters can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data. The hash of the signature of the event is one of the topics except you declared the event with `anonymous` specifier. All non-indexed arguments will be stored in the data part of the log. Example:

```
contract ClientReceipt {
  event Deposit(address indexed _from, bytes32 indexed _id, uint _value);
  function deposit(bytes32 _id) {
    Deposit(msg.sender, _id, msg.value);
  }
}
```
Here, the call to `Deposit` will behave identical to
`log3(msg.value, 0x50cb9fe53daa9737b786ab3646f04d0150dc50ef4e75f59509d83667ad5adb20, sha3(msg.sender), _id);`. Note that the large hex number is equal to the sha3-hash of "Deposit(address,bytes32,uint256)", the event's signature.

## Layout of Storage

Statically-sized variables (everything except mapping and dynamically-sized array types) are laid out contiguously in storage starting from position `0`. Multiple items that need less than 32 bytes are packed into a single storage slot if possible, according to the following rules:

- The first item in a storage slot is stored lower-order aligned.
- Elementary types use only that many bytes that are necessary to store them.
- If an elementary type does not fit the remaining part of a storage slot, it is moved to the next storage slot.
- Structs and array data always start a new slot and occupy whole slots (but items inside a struct or array are packed tightly according to these rules).

The elements of structs and arrays are stored after each other, just as if they were given explicitly.

Due to their unpredictable size, mapping and dynamically-sized array types use a `sha3`
computation to find the starting position of the value or the array data. These starting positions are always full stack slots.

The mapping or the dynamic array itself
occupies an (unfilled) slot in storage at some position `p` according to the above rule (or by
recursively applying this rule for mappings to mappings or arrays of arrays). For a dynamic array, this slot stores the number of elements in the array. For a mapping, the slot is unused (but it is needed so that two equal mappings after each other will use a different hash distribution).
Array data is located at `sha3(p)` and the value corresponding to a mapping key
`k` is located at `sha3(k . p)` where `.` is concatenation. If the value is again a
non-elementary type, the positions are found by adding an offset of `sha3(k . p)`.

So for the following contract snippet:
```
contract c {
  struct S { uint a; uint b; }
  uint x;
  mapping(uint => mapping(uint => S)) data;
}
```
The position of `data[4][9].b` is at `sha3(uint256(9) . sha3(uint256(4) . uint(256(1))) + 1`.

## Esoteric Features

There are some types in Solidity's type system that have no counterpart in the syntax. One of these types are the types of functions. But still, using `var` it is possible to have local variables of these types:

```
contract FunctionSelector {
  function select(bool useB, uint x) returns (uint z) {
    var f = a;
    if (useB) f = b;
    return f(x);
  }
  function a(uint x) returns (uint z) {
    return x * x;
  }
  function b(uint x) returns (uint z) {
    return 2 * x;
  }
}
```

Calling `select(false, x)` will compute `x * x` and `select(true, x)` will compute `2 * x`.


## Internals - the Optimizer

The Solidity optimizer operates on assembly, so it can be and also is used by other languages. It splits the sequence of instructions into basic blocks at points that are hard to move. These are basically all instructions that modify change the control flow (jumps, calls, etc), instructions that have side effects apart from `MSTORE` and `SSTORE` (like `LOGi`, `EXTCODECOPY`, but also `CALLDATALOAD` and others). Inside of such a block, the instructions are analysed and every modification to the stack, to memory or storage is recorded as an expression which consists of an instruction and a list of arguments which are essentially pointers to other expressions. The main idea is now to find expressions that are always equal (or every input) and combine them into an expression class. The optimizer first tries to find each new expression in a list of already known expressions. If this does not work, the expression is simplified according to rules like `constant` + `constant` = `sum_of_constants` or `X` * 1 = `X`. Since this is done recursively, we can also apply the latter rule if the second factor is a more complex expression where we know that it will always evaluate to one. Modifications to storage and memory locations have to erase knowledge about storage and memory locations which are not known to be different: If we first write to location x and then to location y and both are input variables, the second could overwrite the first, so we actually do not know what is stored at x after we wrote to y. On the other hand, if a simplification of the expression x - y evaluates to a non-zero constant, we know that we can keep our knowledge about what is stored at x.

At the end of this process, we know which expressions have to be on the stack in the end and have list of modifications to memory and storage. From these expressions which are actually needed, a dependency graph is created and every operation that is not part of this graph is essentially dropped. Now new code is generated that applies the modifications to memory and storage in the order they were made in the original code (dropping modifications which were found not to be needed) and finally, generates all values that are required to be on the stack in the correct place.

These steps are applied to each basic block and the newly generated code is used as replacement if it is smaller. If a basic block is split at a JUMPI and during the analysis, the condition evaluates to a constant, the JUMPI is replaced depending on the value of the constant, and thus code like
```
var x = 7;
data[7] = 9;
if (data[x] != x + 2) 
  return 2;
else
  return 1;
```
is simplified to code which can also be compiled from
```
data[7] = 9;
return 1;
```
even though the instructions contained a jump in the beginning.
