# Taproot descriptor walllet (manual setup)

As of version 22.0 of Bitcoin Core descriptor wallets are **not** yet automatically initialized with a taproot descriptor. As a result they cannot generate `bech32m` addresses. Here's a way to generate these descriptors manually using `bx` (Libbitcoin Explorer) as an auxiliary tool.

## Descriptor format

As defined in [BIP 380](https://github.com/bitcoin/bips/blob/master/bip-0380.mediawiki#Specification).

`tr([ed493b83/86'/0'/0']xprv9xgqHN7yz9MwCkxsBPN5qetuNdQSUttZNKw1dcYTV4mkaAFiBVGQziHs3NRSWMkCzvgjEe3n9xV8oYywvM8at9yRqyaZVz6TYYhX98VjsUk/0/*)#06sjusfa`

## Generating keys

We will perform this process against the test vectors from [BIP 86](https://github.com/bitcoin/bips/blob/master/bip-0086.mediawiki#Test_vectors).

    TR_MNEMONIC=(abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about)
    TR_SEED=$(bx mnemonic-to-seed $TR_MNEMONIC)
    # 5eb00bbddcf069084889a8ab9155568165f5c453ccb85e70811aaed6f6da5fc19a5ac40b389cd370d086206dec8aa6c43daea6690f20ad3d8d48b2d2ce9e38e4

    bx hd-new 5eb00bbddcf069084889a8ab9155568165f5c453ccb85e70811aaed6f6da5fc19a5ac40b389cd370d086206dec8aa6c43daea6690f20ad3d8d48b2d2ce9e38e4
    # xprv9s21ZrQH143K3GJpoapnV8SFfukcVBSfeCficPSGfubmSFDxo1kuHnLisriDvSnRRuL2Qrg5ggqHKNVpxR86QEC8w35uxmGoggxtQTPvfUu

    bx hd-to-public xprv9s21ZrQH143K3GJpoapnV8SFfukcVBSfeCficPSGfubmSFDxo1kuHnLisriDvSnRRuL2Qrg5ggqHKNVpxR86QEC8w35uxmGoggxtQTPvfUu
    # xpub661MyMwAqRbcFkPHucMnrGNzDwb6teAX1RbKQmqtEF8kK3Z7LZ59qafCjB9eCRLiTVG3uxBxgKvRgbubRhqSKXnGGb1aoaqLrpMBDrVxga8

Calculate fingerprint

As per [BIP 32](https://en.bitcoin.it/wiki/BIP_0032) specification.

    bx base58-decode xpub661MyMwAqRbcFkPHucMnrGNzDwb6teAX1RbKQmqtEF8kK3Z7LZ59qafCjB9eCRLiTVG3uxBxgKvRgbubRhqSKXnGGb1aoaqLrpMBDrVxga8
    # 0488b21e0000000000000000007923408dadd3c7b56eed15567707ae5e5dca089de972e07f3b860450e2a3b70e03d902f35f560e0470c63313c7369168d9d7df2d49bf295fd9fb7cb109ccee0494c7fe61f5

    bx bitcoin160 0488b21e0000000000000000007923408dadd3c7b56eed15567707ae5e5dca089de972e07f3b860450e2a3b70e03d902f35f560e0470c63313c7369168d9d7df2d49bf295fd9fb7cb109ccee0494c7fe61f5
    # ed493b8354c1766c6b0a55c906d34582818baa8b

    echo ed493b8354c1766c6b0a55c906d34582818baa8b | cut -c 1-8
    # ed493b83

Account keys.

    bx hd-private -d -i 86 xprv9s21ZrQH143K3GJpoapnV8SFfukcVBSfeCficPSGfubmSFDxo1kuHnLisriDvSnRRuL2Qrg5ggqHKNVpxR86QEC8w35uxmGoggxtQTPvfUu
    # xprv9ukW2Usuz4vBKZJxKfMUwtjtSTD2U4QxW1ue4prK5TYzYM3voo8EEyUPsyYHvHP8jvj9w4Xr6SAdpEGEDVfpQm8q1puVtRTUidX4mgrouHH

    bx hd-private -d -i 0 xprv9ukW2Usuz4vBKZJxKfMUwtjtSTD2U4QxW1ue4prK5TYzYM3voo8EEyUPsyYHvHP8jvj9w4Xr6SAdpEGEDVfpQm8q1puVtRTUidX4mgrouHH
    # xprv9wMkdjRwYptb4FzqvUNxxehWWywVtgVvrV5X9BKb16bugM5eQJdHLG7dVF3W1r1KkkSHN3s3txMNMEcisTRLK2ogyU4mek8eAPfXkfUqhhG

    bx hd-private -d -i 0 xprv9wMkdjRwYptb4FzqvUNxxehWWywVtgVvrV5X9BKb16bugM5eQJdHLG7dVF3W1r1KkkSHN3s3txMNMEcisTRLK2ogyU4mek8eAPfXkfUqhhG
    # xprv9xgqHN7yz9MwCkxsBPN5qetuNdQSUttZNKw1dcYTV4mkaAFiBVGQziHs3NRSWMkCzvgjEe3n9xV8oYywvM8at9yRqyaZVz6TYYhX98VjsUk

    bx hd-to-public xprv9xgqHN7yz9MwCkxsBPN5qetuNdQSUttZNKw1dcYTV4mkaAFiBVGQziHs3NRSWMkCzvgjEe3n9xV8oYywvM8at9yRqyaZVz6TYYhX98VjsUk
    # xpub6BgBgsespWvERF3LHQu6CnqdvfEvtMcQjYrcRzx53QJjSxarj2afYWcLteoGVky7D3UKDP9QyrLprQ3VCECoY49yfdDEHGCtMMj92pReUsQ


Account 0, first receiving address for path `m/86'/0'/0'/0/0`.

    bx hd-private -i 0 xprv9xgqHN7yz9MwCkxsBPN5qetuNdQSUttZNKw1dcYTV4mkaAFiBVGQziHs3NRSWMkCzvgjEe3n9xV8oYywvM8at9yRqyaZVz6TYYhX98VjsUk
    # xprvA1n4fAv8WWZbfAECqqZsPxRCCaBUqLXF9VdqK1RMAhcyyAoM3fGx6ytPfVrTHMhqLqGLJP4pgLBsQKYb53tnM3vSDPS6U756uWfrF2TpcXS

    bx hd-private -i 0 xprvA1n4fAv8WWZbfAECqqZsPxRCCaBUqLXF9VdqK1RMAhcyyAoM3fGx6ytPfVrTHMhqLqGLJP4pgLBsQKYb53tnM3vSDPS6U756uWfrF2TpcXS
    # xprvA449goEeU9okwCzzZaxiy475EQGQzBkc65su82nXEvcwzfSskb2hAt2WymrjyRL6kpbVTGL3cKtp9herYXSjjQ1j4stsXXiRF7kXkCacK3T

    bx hd-to-public xprvA449goEeU9okwCzzZaxiy475EQGQzBkc65su82nXEvcwzfSskb2hAt2WymrjyRL6kpbVTGL3cKtp9herYXSjjQ1j4stsXXiRF7kXkCacK3T
    # xpub6H3W6JmYJXN49h5TfcVjLC3onS6uPeUTTJoVvRC8oG9vsTn2J8LwigLzq5tHbrwAzH9DGo6ThGUdWsqce8dGfwHVBxSbixjDADGGdzF7t2B

Part of this process can be verified using a second tool like this [Mnemonic Code Converter](https://iancoleman.io/bip39/) (web based).


Schnorr-specific keys.

    bx hd-to-ec xprvA449goEeU9okwCzzZaxiy475EQGQzBkc65su82nXEvcwzfSskb2hAt2WymrjyRL6kpbVTGL3cKtp9herYXSjjQ1j4stsXXiRF7kXkCacK3T
    # 41f41d69260df4cf277826a9b65a3717e4eeddbeedf637f212ca096576479361

    swift run schnorr-tool internal-key 41f41d69260df4cf277826a9b65a3717e4eeddbeedf637f212ca096576479361
    # cc8a4bc64d897bddc5fbc2f670f7a8ba0b386779106cf1223c6fc5d7cd6fc115

    swift run schnorr-tool tweak cc8a4bc64d897bddc5fbc2f670f7a8ba0b386779106cf1223c6fc5d7cd6fc115
    # 2ca01ed85cf6b6526f73d39a1111cd80333bfdc00ce98992859848a90a6f0258

    swift run schnorr-tool output-key cc8a4bc64d897bddc5fbc2f670f7a8ba0b386779106cf1223c6fc5d7cd6fc115 2ca01ed85cf6b6526f73d39a1111cd80333bfdc00ce98992859848a90a6f0258
    # a60869f0dbcf1dc659c9cecbaf8050135ea9e8cdc487053f1dc6880949dc684c
