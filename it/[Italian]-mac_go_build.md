## Installazione con Homebrew

Il modo piu' semplice di installare go-ethereum e' di utilizzare la propria tap Homebrew. Se non avete Homebrew, [installatelo qui](http://brew.sh).

Poi eseguite il seguente comando per aggiungere la tap e installare `geth`:

```shell
brew tap ethereum/ethereum
brew install ethereum
```

Potete installare la branch di sviluppo aggiungendo `--devel`:

```shell
brew install ethereum --devel
```

Per installare `mist`, aggiungete `--with-gui`.

Dopo l'installazione, eseguite `geth` per far partire il vostro nodo.

Per opzioni e patches, guardate: https://github.com/ethereum/homebrew-ethereum

## Compilare dal codice sorgente

### Compilare Geth (il client da linea di comando, CLI)

Clonate la repository Github in una cartella di vostra scelta:

```shell
git clone https://github.com/ethereum/go-ethereum
```

Compilare `geth` richiede l'installazione di alcune librerie esterne:

* [GMP](https://gmplib.org)
* [Go](https://golang.org)

```shell
brew install gmp go
```

Infine, compilate il programma `geth` dando il comando seguente.
```shell
cd go-ethereum
make geth
```

Adesso potete eseguire `build/bin/geth` per far partire il vostro nodo.

### Compilare Mist (GUI)

**Nota: Mist al momento e' in fase di sviluppo percio' il funzionamento non e' garantito.**

Clonate la repository in una cartella qualsiasi:

```shell
git clone https://github.com/ethereum/go-ethereum
```

Compilare `mist` richiede l'installazione di alcune librerie esterne:

* [GMP](https://gmplib.org)
* [Qt 5](https://www.qt.io)
* [Qt 5 WebEngine](http://wiki.qt.io/QtWebEngine)
* [Go](https://golang.org)

Installate in primo luogo le suddette:

```shell
brew install gmp go qt5
brew link -f qt5 # This is required, sorry.
```

Infine compilate `mist` dando il comando seguente:

```shell
cd go-ethereum
make mist
```

Mist non cerca autonomamente nel posto giusto i componenti dell'interfaccia grafica. Potete dunque settare il percorso dei componenti mediante linea di comando.

```shell
build/bin/mist --asset_path cmd/mist/assets
```

Oppure eseguitelo dalla sua directory sorgente:

```shell
cd cmd/mist
../../build/bin/mist
```

