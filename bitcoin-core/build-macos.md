# Building Bitcoin Core on macOS 12 (Monterrey)

## Prepare your environment

This guide was created using macOS version 12.0.1 with Xcode, Homebrew and GPG installed.

Install dependencies. Check [https://github.com/bitcoin/bitcoin/blob/v22.0/doc/build-unix.md] for a detailed list.

    # Setup Autotools (build system).
    brew install automake libtool pkg-config

    # Add required libraries.
    brew install boost libevent

    # Add networking libraries for firewall jumping and notifications.
    brew install miniupnpc libnatpmp zeromq

Get the source code from [https://bitcoincore.org/en/download/].

    curl -l https://bitcoincore.org/bin/bitcoin-core-22.0/bitcoin-22.0.tar.gz | tar -xz

Alternatively…

    git clone -b v22.0 https://github.com/bitcoin/bitcoin

Configure without support for Berkeley DB. 

    ./autogen.sh
    ./configure --without-bdb

Run the tests.

    make check

Create a configuration for testnet.

    mkdir ~"/Library/Application Support/Bitcoin"
    cat << EOF >> ~"/Library/Application Support/Bitcoin/bitcoin.conf" 
    daemon=1
    server=1
    testnet=1
    txindex=1
    disablewallet=1
    EOF

Start the daemon and check the debug log using the Console app.

    src/bitcoind
    src/bitcoin-cli -getinfo
    open -a Console.app ~"/Library/Application Support/Bitcoin/testnet3/debug.log"
