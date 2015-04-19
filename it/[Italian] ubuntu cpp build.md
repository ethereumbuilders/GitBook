Qui sotto ci sono le istruzioni per compilare il client in C++ di Ethereum sull'ultima release di Ubuntu. Al Dicembre 2014, la migliore piattaforma su cui installare e' Ubuntu 14.04 (trusty), 64 bit, con almeno 2 GB di RAM. Tutti i nostri test sono stati condotti su questa versione. Qualsiasi contributo della community per testare su altre versioni e' benvenuto!

# Guida per Ubuntu 14.04, 64 bit

## Installare le dependencies

Prima di compilare il codice sorgente, avete bisogno di alcuni strumenti e di diverse dependencies affinche' l'applicazione possa partire.

In primo luogo, aggiornate i repositories. Nella repository principale di Ubuntu non ci sono tutti i pacchetti, e per questo avete bisogno di prenderli dalla PPA di Ethereum e dall'archivio LLVM:

```
sudo apt-get -y update
sudo apt-get -y install language-pack-en-base
sudo dpkg-reconfigure locales
sudo apt-get -y install software-properties-common
wget -O - http://llvm.org/apt/llvm-snapshot.gpg.key | sudo apt-key add -
sudo add-apt-repository "deb http://llvm.org/apt/trusty/ llvm-toolchain-trusty-3.5-binaries main"
sudo add-apt-repository -y ppa:ethereum/ethereum-qt
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo add-apt-repository -y ppa:ethereum/ethereum-dev
sudo apt-get -y update
sudo apt-get -y upgrade
```

Dovete installare i seguenti pacchetti di sviluppo se volete compilare la suite completa, inclusi i componenti a interfaccia grafica:
```
sudo apt-get -y install build-essential g++-4.8 git cmake libboost-all-dev automake unzip libgmp-dev libtool libleveldb-dev yasm libminiupnpc-dev libreadline-dev scons libncurses5-dev libcurl4-openssl-dev wget qtbase5-dev qt5-default qtdeclarative5-dev libqt5webkit5-dev libqt5webengine5-dev libcryptopp-dev libjson-rpc-cpp-dev libmicrohttpd-dev libjsoncpp-dev libargtable2-dev clang-3.5 lldb-3.5
```

Facoltativo: Se volete solo compilare in modalita' linea di comando (cioe' senza interfaccia grafica), non avete bisogno delle dependencies specifiche per Qt, quindi invece del comando precedente, lanciate:
```
sudo apt-get -y install build-essential g++-4.8 git cmake libboost-all-dev automake unzip libgmp-dev libtool libleveldb-dev yasm libminiupnpc-dev libreadline-dev scons libncurses5-dev libcurl4-openssl-dev wget libjsoncpp-dev libargtable2-dev libcryptopp-dev libjson-rpc-cpp-dev libmicrohttpd-dev clang-3.5 lldb-3.5
```

## Scegliete la sorgente

Per prima cosa prendete e decomprimete i sorgenti. Se volete compilare l'ultima versione, clonate la repository:

```
git clone https://github.com/ethereum/cpp-ethereum
cd cpp-ethereum
git checkout develop 
```

Se avete una sorgente preimpacchettata, allora semplicemente decomprimete:
```
tar xjf cpp-ethereum-<version>.tar.bz2
cd cpp-ethereum-<version>
```

## Compilazione

Create e configurate l'ambiente di compilazione e compilate all'interno della directory di cpp-ethereum:
```
mkdir build
cd build
cmake ..
```

Facoltativo: Potete configurare la compilazione in diversi modi semplicemente passando dei determinati parametri al comando cmake di cui sopra:
```
cmake .. -DCMAKE_BUILD_TYPE=Release -DBUNDLE=minimal # Compile minimal amount, just enough for a node, enable compiler optimsations.
cmake .. -DCMAKE_BUILD_TYPE=Debug -DBUNDLE=user -DFATDB=1 # Compile enough for normal usage and with support for the full chain explorer
cmake .. -DBUNDLE=full -DGUI=0 # builds only the commandline-clients; no need to bother with the Qt dependencies.
```


Adesso, compilate il sourcetree: 
```
make -j2
```

Potete anche incrementare `2` a `4` o persino a `8` per rendere la compilazione considerabilmente piu' veloce su dei computer decenti.


## Avviate il client client

Client a interfaccia su linea di comando (vedete [[Using Ethereum CLI Client]]):

```
cd ~/cpp-ethereum/build/eth
./eth
```

Una volta fatto, potete a questo punto [[Configurare un server]] e avviare una [[Test Net Locale]].

Facoltativo: Per testare che l'interfaccia JSON RPC funziona, lanciate il comando `eth -j`, e monitorate la coinbase con cURL:
```
curl -X POST --data '{"jsonrpc": "2.0","method": "eth_coinbase","params": null,"id": 1}' http://localhost:8080
```

Avviate AlethZero per aprire il client sperimentare a interfaccia grafica (vedete anche [[Usare AlethZero]]).

```
cd ~/cpp-ethereum/build/alethzero
./alethzero
```
Se state aggiornando da una versione precedente e vi vengono segnalati errori durante l'esecuzione, cancellate la vostra vecchia block chain prima di riavviare:

```
rm -rf ~/.ethereum
```

Facoltativo: per attivare il supporto per la unita' di test comune di Ethereum (CEUT), clonate i test repository allo stesso percorso di cpp-ethereum e fate il checkout del ramo di sviluppo:

```
git clone https://github.com/ethereum/tests
cd tests
git checkout develop
cd ..
```

# Compilare e installare un nodo server di cpp-ethereum su Ubuntu 14.04, 64 bit

Copiate e incollate quanto segue su una riga di comando:

```
wget http://opensecrecy.com/setupbuildeth.sh && source ./setupbuildeth.sh BRANCH NODE_IP NODE_NAME && rm -f setupbuildeth.sh && reboot
```

- `BRANCH` dovrete sostituire con sia `master` o `develop`, a seconda se vogliate una versione stabile o bleeding-edge .
- `NODE_IP` dovrete sostituire questo con l'indirizzo IP del nodo, che contiene quattro numeri delimitati da un punto. Ad esempio `1.2.3.4` o `192.168.1.69`.
- `NODE_NAME` dovrete sostituire questo con il nome del nodo, tra virgolette se contiene degli spazi. Evitate di utilizzare simboli. Ad esempio `"Gavs Server Node"` o `Release_Node_1`.

Aspettate che si riavvii e starete operando un nodo.



# Istruzioni per altre versione


**Da Dicembre 2014, abbiamo cambiato il sistema di compilazione. Le seguenti istruzioni non sono state testate. Ogni contribuzione per verificare o migliorare il processo di compilazione per queste versioni e' benvenuta.**

## Ubuntu 14.04 con installazione manuale di Qt

#### Web Install Qt version >= 5.4
Qt e' per l'interfaccia grafica (GUI). Non ne avete bisogno per l'interfaccia a linea di comando. 
Potete scaricarlo dal loro [sito](http://qt-project.org/downloads), o utilizzare:

```
# Per una 32-bit:
wget http://download.qt-project.org/official_releases/online_installers/qt-opensource-linux-x86-online.run
# Per una 64-bit:
wget http://download.qt-project.org/official_releases/online_installers/qt-opensource-linux-x64-online.run
```

Fatelo girare con:
```
chmod +x qt-opensource-linux-???-online.run
./qt-opensource-linux-???-online.run
```
Quando l'installer vi chiede qual e' la versione desiderata, installate almeno una versione 5.4 o superiore. Appuntatevi la directory di installazione. 

Per preparare l'ambiente per la nuova installazione di Qt (e impedire che usi una versione precedente dell'installazione di Qt), diremo al comando cmake che preferiamo questa versione:
```
export CMAKE_PREFIX_PATH=<Qt installation directory>/Qt/5.*/gcc
```



## Wheezy 13.04 e Saucy 13.10

Qui i pacchetti necessari sono piu' o meno gli stessi, ma dato che alcune dependencies non sono disponibili per queste versioni di Ubuntu, il processo di installazione e' un po' diverso:

```
sudo apt-get install build-essential g++-4.8 git cmake libboost1.53-all-dev # for build
sudo apt-get install libncurses5-dev automake libtool unzip libgmp-dev libleveldb-dev yasm libminiupnpc-dev libreadline-dev scons # for ethereum
sudo apt-get install libcurl4-openssl-dev # for json-rpc serving client
```

## Precise 12.04
La compilazione e installazione su questa versione e' abbastanza problematica, con un sacco di grattacapi. E' altamente consigliato che installiate la versione 14.04 (che e' anche una versione Long Term Support). Ad ogni modo, una review (che si propone di essere completa) dei possibili problemi puo' essere trovata alla pagina "[Compatibility Info and Build Tips](https://github.com/ethereum/cpp-ethereum/wiki/Compatibility-Info-and-Build-Tips)" di questa wiki (in inglese). 

Altri avvisi:
Per questa release abbiamo bisogno di alcune backports. Vedete http://www.swiftsoftwaregroup.com/upgrade-gcc-4-7-ubuntu-12-04/ per GCC 4.7, che e' richiesto. Avrete bisogno di queste PPAs e di queste librerie libleveldb:
```
sudo apt-add-repository ppa:ubuntu-sdk-team/ppa && sudo apt-add-repository ppa:apokluda/boost1.53
wget http://launchpadlibrarian.net/148520969/libleveldb-dev_1.13.0-1_amd64.deb && wget http://launchpadlibrarian.net/148520968/libleveldb1_1.13.0-1_amd64.deb && sudo dpkg -i libleveldb*1.13.0-1*deb
```
Piu' informazioni su "[Compatibility Info and Build Tips](https://github.com/ethereum/cpp-ethereum/wiki/Compatibility-Info-and-Build-Tips)" di questa wiki (in inglese). 
