# Making Bitcoin-Core RPC Calls using cURL

Get the authentication cookie.

    cat ~/Library/Application\ Support/Bitcoin/testnet3/.cookie
    # __cookie__:d0d9af58a2d4567283eb781564d994893d280dec84a0d8eb5046d93b2c03c8ff%

Ignore the final '%' in the output.

Invoke cURL.

    curl --data-binary '{"jsonrpc":"1.0","id":"curltext","method":"getblockchaininfo","params":[]}' -H 'content-type:text/plain;' --user __cookie__:d0d9af58a2d4567283eb781564d994893d280dec84a0d8eb5046d93b2c03c8ff http://127.0.0.1:18332/

You will get a JSON response.

```json
{"result":{"chain":"test","blocks":2106208,"headers":2106208,"bestblockhash":"00000000dc011edc4217043cc14d6754ccda2ecfeb40e08a07b7a74a4e71f9a0","difficulty":1,"mediantime":1638608312,"verificationprogress":0.9999984658522834,"initialblockdownload":false,"chainwork":"00000000000000000000000000000000000000000000061b4ca4e23bf8d0ae06","size_on_disk":30136350208,"pruned":false,"softforks":{"bip34":{"type":"buried","active":true,"height":21111},"bip66":{"type":"buried","active":true,"height":330776},"bip65":{"type":"buried","active":true,"height":581885},"csv":{"type":"buried","active":true,"height":770112},"segwit":{"type":"buried","active":true,"height":834624},"taproot":{"type":"bip9","bip9":{"status":"active","start_time":1619222400,"timeout":1628640000,"since":2011968,"min_activation_height":0},"height":2011968,"active":true}},"warnings":"Unknown new rules activated (versionbit 28)"},"error":null,"id":"curltext"}
```

## Avoiding explicit use of the cookie value

To get a secure password promps instead.

    curl --data-binary '{"jsonrpc":"1.0","id":"curltext","method":"getblockchaininfo","params":[]}' -H 'content-type:text/plain;' --user __cookie__ http://127.0.0.1:18332/
    # Enter host password for user '__cookie__':

Or read directly from the cookie file and onto the cURL command.

    curl --data-binary '{"jsonrpc":"1.0","id":"curltext","method":"getblockchaininfo","params":[]}' -H 'content-type:text/plain;' --user "$(cat ~/Library/Application\ Support/Bitcoin/testnet3/.cookie)" http://127.0.0.1:18332/
