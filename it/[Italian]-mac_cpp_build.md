Nonostante Mavericks sia richiesto per compilare l'app, l'app e' stata testata per girare su 10.8, 10.9 e 10.10. 

## Compilazione con Homebrew

Per la release stabile:
```
brew tap ethereum/ethereum
brew install cpp-ethereum --build-from-source
brew linkapps cpp-ethereum
```

Per la release di sviluppo:
```
brew reinstall cpp-ethereum --devel --build-from-source
brew linkapps cpp-ethereum
```

E con l'opzione `--with-gui` potete anche compilare AlethZero e la suite di sviluppo Mix.

Poi fate `open /Applications/AlethZero.app`, `eth` (CLI) o `neth` (interfaccia ncurses)

Per opzioni e patches, guardate: https://github.com/ethereum/homebrew-ethereum

## Compilazione manuale

### Prerequisiti
* Il piu' recente Xcode su Mavericks (almeno Xcode 5.1+).
* Il manager di pacchetti Homebrew  http://brew.sh *o* il manager di pacchetti MacPorts  http://macports.org
* XQuartz  http://xquartz.macosforge.org/landing/

### Istallate le dependencies

Utilizzando homebrew:

    brew install boost --c++11 # this takes a while
    brew install boost-python --c++11
    brew install cmake qt5 cryptopp miniupnpc leveldb gmp libmicrohttpd libjson-rpc-cpp

O, utilizzando MacPorts:

    port install boost +universal # this takes a while
    port install cmake qt5-mac libcryptopp miniupnpc leveldb gmp
    (probably missing libmicrohttpd and libjson-rpc-cpp)

### Clonate il repository della sorgente e create una cartella di compilazione
    git clone https://github.com/ethereum/cpp-ethereum.git
    cd cpp-ethereum
    mkdir -p build # create build folder ('build' ignored by git)

### Compilate
    cd build
    cmake ..
    make -j6
    make install

Questo installera' anche il cliente a linea di comando e le librerie in /usr/local.

### Istruzioni XCode
Dalla cartella root del progetto:
```
mkdir build_xc
cd build_xc
cmake -G Xcode ..
```
Questo generera' un project file xcode insieme a delle configurazioni per voi: ethereum.xcodeproj . Aprite questo file in XCode e dovrete essere in grado di compilare il progetto. 

## Troubleshooting

Errori di collegamento possono essere generalmente risolti facendosi sicuri di avere gli indirizzi corretti:

    export LIBRARY_PATH=/opt/local/lib # this is MacPorts's default

Fate in modo che il vostro `macdeployqt` puo' essere trovato. Ethereum si aspetta che sia in `/usr/local/opt/qt5/bin/macdeployqt` quindi fate un symlink se non c'e':

    ln -s `which macdeployqt` /usr/local/opt/qt5/bin/macdeployqt

Se non pianificate di partecipare allo sviluppo, fate in modo di compilare dal ramo master, non da quello develop o altri.  Questi rami spesso possono essere rotti o avere altri requisiti.
