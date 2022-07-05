## Create Payment Addresses
> The naming convention for address files below is personal choice.  
> All of these address are created under a folder addresses 
```bash
cardano-cli address key-gen \
    --verification-key-file=addr1.payment.vk.json \
    --signing-key-file=addr1.payment.sk.json
```
## Create Staking Addresses
```bash
cardano-cli stake-address key-gen \
    --verification-key-file=addr1.staking.vk.json \
    --signing-key-file=addr1.staking.sk.json
```
## Create Shelley Base Address for Testnet
```bash
cardano-cli address build ${CARDANO_TESTNET} \
    --payment-verification-key-file=addr1.payment.vk.json \
    --stake-verification-key-file=addr1.staking.vk.json \
    --out-file=addr1.addr1.shelley
```
## List UTXOs of a Shelley Base Address for Testnet
```bash
cardano-cli query utxo ${CARDANO_TESTNET} --address="$(cat addr1.base.shelley)"
```
## Use to faucet to get some tADA to your base address and try listing UTXOs again 

## Build another address to be able to send some tADA via a custom tx
```bash
cardano-cli address key-gen \
    --verification-key-file=addr2.payment.vk.json \
    --signing-key-file=addr2.payment.sk.json
```
## Build Base address using the addr2 and previous stake address.
```bash
cardano-cli address build ${CARDANO_TESTNET} \
    --payment-verification-key-file=addr2.payment.vk.json \
    --stake-verification-key-file=addr1.staking.vk.json \
    --out-file=addr2.base.shelley
```