This sample tries to explain how to submit a more complex transaction where two utxos from DIFFERENT users - user1, user2 can be utilized as inputs to pay out to a user3.

The transaction requires use of the witness-override and witness signing feature of cardano-cli

High level steps

query utxo for user 1, create environment variable UTXO1 var in prompt with txid#index query utxo for user 2, create utxo2 var in prompt with txid#index

build transaction with two tx-in, one tx-out to user3 and change-address to user2 (for example) use the --witness-override 2 --out-file txmultutxo.raw

transaction view --tx-body-file txmultutxo.raw

witness the transaction using below command for both user1, user2 cardano-cli transaction witness --signing-key-file user1.skey --tx-body-file txmultutxo.raw --out-file user1.witness $TESTNET

cardano-cli transaction assemble --tx-body-file txmultutxo.raw --witness-file user1.witness --witness-file user2.witness --out-file txmultutxo.signed

cardano-cli transaction submit --tx-file txmultutxo.signed $TESTNET

Steps:

cardano-cli query utxo ${CARDANO_TESTNET} --address="$(cat addr1.base.shelley)"

UTXO_IN_1=<TX_ID>#<TX_IX>

cardano-cli query utxo ${CARDANO_TESTNET} --address="$(cat addr2.base.shelley)"

UTXO_IN_2=<TX_ID>#<TX_IX>

cardano-cli transaction build --babbage-era ${CARDANO_TESTNET} --witness-override 2 --tx-in=$UTXO_IN_1 --tx-in=$UTXO_IN_2 --tx-out="$(cat addr3.base.shelley)+500000000" --change-address="$(cat addr1.base.shelley)" --out-file="../txs/utxotx.raw.json"

cardano-cli transaction witness --tx-body-file="../txs/utxotx.raw.json" --signing-key-file="addr1.payment.sk.json" ${CARDANO_TESTNET} --out-file="../txs/addr1.witness.json"

cardano-cli transaction witness --tx-body-file="../txs/utxotx.raw.json" --signing-key-file="addr2.payment.sk.json" ${CARDANO_TESTNET} --out-file="../txs/addr2.witness.json"

cardano-cli transaction assemble --tx-body-file="../txs/utxotx.raw.json" --witness-file="../txs/addr1.witness.json" --witness-file="../txs/addr2.witness.json" --out-file="../txs/utxotx.assembled.json"

cardano-cli transaction submit --tx-file="../txs/utxotx.assembled.json" ${CARDANO_TESTNET}

cardano-cli transaction txid --tx-file="../txs/utxotx.assembled.json"

