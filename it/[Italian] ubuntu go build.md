## Opzione 1: Installazione da PPA

Per avere la piu' recente versione in sviluppo, sia `ppa:ethereum/ethereum` che `ppa:ethereum/ethereum-dev` sono necessarie. Se voleste la versione stabile della release relativa all'ultima PoC (Prova di Concetto), omettete semplicemnte la PPA `-dev`.

**Attenzione: La PPA `ethereum-qt` aggiornera' l'installazione Qt5 del vostro sistema, dalla 5.2 su Trusty e dalla 5.3 su Utopic, alla 5.4.*

```
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ethereum/ethereum-qt
sudo add-apt-repository ppa:ethereum/ethereum
sudo add-apt-repository ppa:ethereum/ethereum-dev
sudo apt-get update
sudo apt-get install ethereum
```

Adesso scrivete `mist` per l'interfaccia grafica o `geth` per la linea di comando.

In alternativa, potete installare solamente la interfaccia a linea di comando o solo l'interfaccia grafica, rispettivamente tramite `apt-get install geth` o `apt-get install mist`.

## Opzione 2: Installazione automatico

**Nota** Obsoleto, per favore usate le PPA.

[Lo script di installazione di Mist](https://gist.github.com/tgerring/d4ab3f1672ed91a53c6c) installera' tutto cio' che e' necessario su una installazione di Ubuntu 14.04 ed iniziera' l'esecuzione di Mist.

```
wget https://gist.githubusercontent.com/tgerring/d4ab3f1672ed91a53c6c/raw/677a3dd9c6db099eee620657bf7fb1e664173ee1/mist-develop.sh -O install
chmod +x install 
./install
```

## Opzione 3: Compilazione manuale dai codici sorgenti

### Installare Go

Verificate che Go e' installato scrivendo `go version` e controllando la versione. Se non lo e', controllate [Installing Go](https://github.com/ethereum/go-ethereum/wiki/Installing-Go) (in inglese)

### Prerequisiti

Mist dipende dalle seguenti librerie esterne, che devono essere installate:
* [GMP](https://gmplib.org)
* [Readline](http://www.gnu.org/s/readline/)
* [Qt 5.4](http://www.qt.io/download-open-source/).

Innanzitutto installate GMP & Readline dalle loro repositories:
```
sudo apt-get install -y libgmp3-dev libreadline6-dev
```
Dunque, seguite le istruzioni per [Building Qt](https://github.com/ethereum/go-ethereum/wiki/Building-Qt) (in inglese)

### Installare Mist
Adesso che avete finito con i prerequisiti, i comando seguenti compileranno il client a interfaccia grafica Mist:

    go get -u github.com/ethereum/go-ethereum/cmd/mist

(Probabilmente avrete bisogno di lanciare prima il comando "sudo apt-get install mercurial")

**Nota**: Mist non cerca automaticamente nel posto giusto per i componenti dell'interfaccia grafica. Percio' dovete lanciare questo comando dalla sua directory di compilazione.

    cd $GOPATH/src/github.com/ethereum/go-ethereum/cmd/mist && mist

o altrimenti fornite al sistema un'opzione assoluta `-asset_path`:

    mist -asset_path $GOPATH/src/github.com/ethereum/go-ethereum/cmd/mist/assets

Per rimuovere il bisogno di ricordare questo complicato comando, potete create quanto segue in un file su $GOPATH/bin :

    #!/bin/bash
    cd $GOPATH/src/github.com/ethereum/go-ethereum/cmd/mist && mist

Chiamate il file 'misted' e rendetelo eseguibile:

    chmod +x $GOPATH/bin/misted

Adesso Mist puo' essere lanciato con il comando `misted`
