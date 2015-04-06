Serpent is one of the high-level programming languages used to write Ethereum contracts. The language, as suggested by its name, is designed to be very similar to Python; it is intended to be maximally clean and simple, combining many of the efficiency benefits of a low-level language with ease-of-use in programming style, and at the same time adding special domain-specific features for contract programming. The latest version of the Serpent compiler, available [on github](http://github.com/ethereum/serpent), is written in C++, allowing it to be easily included in any client.

This tutorial assumes basic knowledge of how Ethereum works, including the concept of blocks, transactions, contracts and messages and the fact that contracts take a byte array as input and provide a byte array as output. If you do not, then go [here](https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial) for a basic tutorial.

### Differences Between Serpent and Python

The important differences between Serpent and Python are:

* Python numbers have potentially unlimited size, Serpent numbers wrap around 2<sup>256</sup>. For example, in Serpent the expression `3^(2^254)` suprisingly evaluates to 1, even though in reality the actual integer is too large to be recorded in its entirety within the universe.
* Serpent has no decimals.
* Serpent has no concept of strings (at this point)
* Serpent has no list comprehensions (expressions like `[x**2 for x in my_list]`), dictionaries or most other advanced features
* Serpent has no concept of first-class functions. Contracts do have functions, and can call their own functions, but variables (except storage) do not persist across calls.
* Serpent has a concept of persistent storage variables (see below)
* Serpent has an `extern` statement used to call functions from other contracts (see below)

### Installation

In order to install the Serpent python library and executable do:

    sudo pip install ethereum-serpent

If you want a library you can directly call from C++, instead do:

    git clone http://github.com/ethereum/serpent
    cd serpent
    make
    sudo make install

