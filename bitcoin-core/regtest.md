# Bitcoin Core Regtest Network

Example `bitcoin.conf` for node `10.0.0.2`.

    regtest=1
    txindex=1
    server=1
    rpcallowip=10.0.0.0/16
    
    [regtest]
    addnode=10.0.0.3
    rpcbind=0.0.0.0

Example `bitcoin.conf` for node `10.0.0.3`.

    regtest=1
    txindex=1
    server=1
    rpcallowip=10.0.0.0/16

    [regtest]
    addnode=10.0.0.2
    rpcbind=0.0.0.0
