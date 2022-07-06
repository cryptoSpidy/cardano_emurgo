## Build Raw Transaction for Testnet
> The naming convention for transaction files below is personal choice.  

- Transaction input is a concatenation of `TxHash`, number sign (`#`) and `TxIx`, obtained from query UTXO.  
- Transaction output is a concatenation of recipient address, sign (`+`) and transaction amount in Lovelace.  
- Change address receives the remaining balance of the input UTXO(s) after deducting the transaction amount plus fees.  
```bash
cardano-cli transaction build ${CARDANO_TESTNET} --babbage-era --out-file="tx1.raw.json" --tx-in='<UTXO-TransactionHash>#0' --tx-out="$(cat addr2.base.shelley)+250000000" --change-address="$(cat addr1.base.shelley)"
```
Check the body output in cbor.me

## Sign Transaction for Testnet
```bash
cardano-cli transaction sign ${CARDANO_TESTNET} --signing-key-file=addr1.payment.sk.json --tx-body-file=tx1.raw.json --out-file=tx1.signed.json 
```
Check the body output in cbor.me

## Submit Transaction to Testnet
```bash
cardano-cli transaction submit ${CARDANO_TESTNET} --tx-file=tx1.signed.json
```
## Get Transaction ID from Testnet
```bash
cardano-cli transaction txid --tx-file=tx1.signed.json
```
Use this txhash to find the status from the testnet