# Regtest configuration parameters

## Bitcoin Explorer configuration file.

Example mainnet `bx.cfg` configuration.

    [wallet]
    # The wallet import format (WIF) key version, defaults to 128 (use 239 for testnet).
    wif_version = 128
    # The hierarchical deterministic (HD) public key version, defaults to 76067358 (use 70617039 for testnet).
    hd_public_version = 76067358
    # The hierarchical deterministic (HD) private key version, defaults to 76066276 (use 70615956 for testnet).
    hd_secret_version = 76066276
    # The pay-to-public-key-hash address version, defaults to 0 (use 111 for testnet).
    pay_to_public_key_hash_version = 0
    # The pay-to-script-hash address version, defaults to 5 (use 196 for testnet).
    pay_to_script_hash_version = 5
    # The transaction version, defaults to 1.
    transaction_version = 1
    # The rule fork flags, defaults to all (4294967295).
    rule_fork_flags = 4294967295

    [network]
    # The magic number for message headers, defaults to 3652501241 (use 118034699 for testnet).
    identifier = 3652501241
    # The number of times to retry contacting a node, defaults to 0.
    connect_retries = 0
    # The time limit for connection establishment, defaults to 5.
    connect_timeout_seconds = 5
    # The time limit to complete the connection handshake, defaults to 30.
    channel_handshake_seconds = 30
    # The peer hosts cache file path, defaults to 'hosts.cache'.
    hosts_file = hosts.cache
    # The debug log file path, defaults to 'debug.log'.
    debug_file = debug.log
    # The error log file path, defaults to 'error.log'.
    error_file = error.log
    # A seed node for initializing the host pool, multiple entries allowed, defaults shown.
    seed = mainnet1.libbitcoin.net:8333
    seed = mainnet2.libbitcoin.net:8333
    seed = mainnet3.libbitcoin.net:8333
    seed = mainnet4.libbitcoin.net:8333
    # Testnet seed nodes.
    #seed = testnet1.libbitcoin.net:18333
    #seed = testnet2.libbitcoin.net:18333
    #seed = testnet3.libbitcoin.net:18333
    #seed = testnet4.libbitcoin.net:18333

    [server]
    # The URL of the default hidden testnet libbitcoin query service.
    #url = tcp://rmrai2ifbed2bf55.onion:19091
    # The URL of the default hidden testnet libbitcoin block service.
    #block_url = tcp://rmrai2ifbed2bf55.onion:19093
    # The URL of the default hidden testnet libbitcoin transaction service.
    #transaction_url = tcp://rmrai2ifbed2bf55.onion:19094
    # The URL of the default testnet libbitcoin query service.
    #url = tcp://testnet.libbitcoin.net:19091
    # The URL of the default testnet libbitcoin block service.
    #block_url = tcp://testnet.libbitcoin.net:19093
    # The URL of the default testnet libbitcoin transaction service.
    #transaction_url = tcp://testnet.libbitcoin.net:19094
    # The URL of the default hidden mainnet libbitcoin query service.
    #url = tcp://sqax52n5enkw4dsj.onion:9091
    # The URL of the default hidden mainnet libbitcoin block service.
    #block_url = tcp://sqax52n5enkw4dsj.onion:9093
    # The URL of the default hidden mainnet libbitcoin transaction service.
    #transaction_url = tcp://sqax52n5enkw4dsj.onion:9094
    # The URL of the default mainnet libbitcoin query service.
    url = tcp://mainnet.libbitcoin.net:9091
    # The URL of the default mainnet libbitcoin block service
    block_url = tcp://mainnet.libbitcoin.net:9093
    # The URL of the default mainnet libbitcoin transaction service
    transaction_url = tcp://mainnet.libbitcoin.net:9094

    # The address of a SOCKS5 proxy, defaults to none.
    socks_proxy = 0.0.0.0:0
    # The number of times to retry contacting a server, defaults to 0.
    connect_retries = 0
    # The time limit for connection establishment, defaults to 5.
    connect_timeout_seconds = 5
    # The Z85-encoded public key of the server, defaults to none.
    #server_public_key =
    # The Z85-encoded private key of the client, defaults to none.
    #client_private_key =

Example testnet `bx.testnet.cfg` configuration.

    [wallet]
    wif_version = 239
    hd_public_version = 70617039
    hd_secret_version = 70615956
    pay_to_public_key_hash_version = 111
    pay_to_script_hash_version = 196

    [network]
    identifier = 118034699
    # Testnet seed nodes.
    #seed = testnet1.libbitcoin.net:18333
    #seed = testnet2.libbitcoin.net:18333
    #seed = testnet3.libbitcoin.net:18333
    #seed = testnet4.libbitcoin.net:18333

    [server]
    # The URL of the default hidden testnet libbitcoin query service.
    #url = tcp://rmrai2ifbed2bf55.onion:19091
    # The URL of the default hidden testnet libbitcoin block service.
    #block_url = tcp://rmrai2ifbed2bf55.onion:19093
    # The URL of the default hidden testnet libbitcoin transaction service.
    #transaction_url = tcp://rmrai2ifbed2bf55.onion:19094
    # The URL of the default testnet libbitcoin query service.
    #url = tcp://testnet.libbitcoin.net:19091
    # The URL of the default testnet libbitcoin block service.
    #block_url = tcp://testnet.libbitcoin.net:19093
    # The URL of the default testnet libbitcoin transaction service.
    #transaction_url = tcp://testnet.libbitcoin.net:19094

## Regtest parameters

Example regtest `bx.regtest.cfg` configuration parameters. Some match testnet, some don't.

    [wallet]
    wif_version = 239 # Same as testnet
    hd_public_version = 70617039
    hd_secret_version = 70615956
    pay_to_public_key_hash_version = 111  # Same as testnet
    pay_to_script_hash_version = 196 # Same as testnet

    [network]
    identifier = 3669344250 # Different from testnet

    # The port for incoming connections.
    inbound_port = 18445 # Different from testnet

    # Disable seeding, otherwise manually populate hosts.cache file.
    host_pool_capacity = 0

    # Optionally connect to one or more peers on a private regtest network.
    peer = localhost:18444

    [blockchain]
    checkpoint = 0f9188f13cb7b2c71f2a335e3a4fc328bf5beb436012afca590b1a11466e2206:0 # Different from testnet

    [fork]
    # Retarget difficulty, defaults to true (use false for regtest).
    retarget = false

    # Require coinbase input includes block height (disabled on satoshi client).
    bip34 = false

## Additional information about regtest network

Regtest information from Bitcoin Core.

    # templates for valid bech32 sequences
    # hrp, version, witprog_size, metadata, encoding, output_prefix
    ('bcrt',  0, 20, (False, 'regtest', None, True), Encoding.BECH32,  p2wpkh_prefix),
    ('bcrt',  0, 32, (False, 'regtest', None, True), Encoding.BECH32,  p2wsh_prefix),
    ('bcrt',  1, 32, (False, 'regtest', None, True), Encoding.BECH32M, p2tr_prefix),
    ('bcrt', 16, 40, (False, 'regtest', None, True), Encoding.BECH32M, (OP_16, 40))

    #regtest default
    port=18443
    netmagic=fabfb5da # (4206867930)
    genesis=0f9188f13cb7b2c71f2a335e3a4fc328bf5beb436012afca590b1a11466e2206
    input=/home/example/.bitcoin/regtest/blocks
    
    # Genesis block time (regtest)
    TIME_GENESIS_BLOCK = 1296688602
    VB_PERIOD = 144           # versionbits period length for regtest
    VB_THRESHOLD = 108        # versionbits activation threshold for regtest

    consensus.nMinerConfirmationWindow = 144; // Faster than normal for regtest (144 instead of 2016)

    param         regtest
    port          18444
    bech32        bcrt
    privkey       0xef
    xpub          tpub
    xpriv         tpub
    pubkeyhash    0x6f
    scripthash    0xc4
