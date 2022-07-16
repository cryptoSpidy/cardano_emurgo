
Have couple of address generated

Save some environment variables

```
TOKEN_NAME=$(echo -n "Testtoken" | xxd -ps | tr -d '\n')
TOKEN_AMOUNT="1000"
ADDRESS_1=$( cat addr1.base.shelley)
ADDRESS_2=$( cat addr2.base.shelley)
```

Download the protocol parameters

```
cardano-cli query protocol-parameters $CARDANO_TESTNET --out-file protocol_params.json
```

##Genrating policy

```
mkdir policy
cardano-cli address key-gen \
    --verification-key-file policy/policy.vk.json \
    --signing-key-file policy/policy.sk.json
```
Create policy.script, where keyHash is the hash of the policy vkey. along with the type.
Any other policy parameters definfing the token should go here, before generating the policyID

```
{
  "keyHash": "17b05970998a1d4e703317f3d0688cca2369b31d0f18d2c419e57057",
  "type": "sig"
}
```
Genrate the policy ID

```
cardano-cli transaction policyid --script-file ./policy/policy.script > policy/policyID
POLICY_ID=545ed63cc00fa4fa6a847fbce2dd00c9123eb419802d48ee938efbd2
```

Fetch the UTXO to be consumed
```
cardano-cli query utxo --address $ADDRESS_1 $CARDANO_TESTNET
```
Fee placeholder
```
FEE="300000"
AMOUNT="10000000"
FUNDS=499657602
```
Draft tx
```
cardano-cli transaction build-raw \
 --fee $FEE \
 --tx-in <txhash>#<txix> \
 --tx-out $ADDRESS_1+$AMOUNT+"$TOKEN_AMOUNT $POLICY_ID.$TOKEN_NAME" \
 --mint="$TOKEN_AMOUNT $POLICY_ID.$TOKEN_NAME" \
 --minting-script-file policy/policy.script \
 --out-file mint.draft
 ```

Calculate fee
```
fee=$(cardano-cli transaction calculate-min-fee --tx-body-file mint.draft --tx-in-count 1 --tx-out-count 1 --witness-count 2 $CARDANO_TESTNET --protocol-params-file protocol_params.json | cut -d " " -f1)
```
Calculate Output ADA Amount

AMOUNT= $(expr $FUNDS - $FEE)

Raw tx with fees and output amounts calculated
```
cardano-cli transaction build-raw \
 --fee $FEE \
 --tx-in <txhash>#<txix> \
 --tx-out $ADDRESS_1+$AMOUNT+"$TOKEN_AMOUNT $POLICY_ID.$TOKEN_NAME" \
 --mint="$TOKEN_AMOUNT $POLICY_ID.$TOKEN_NAME" \
 --minting-script-file policy/policy.script \
 --out-file mint.raw
 ```

Sign the transaction
```
cardano-cli transaction sign \
--signing-key-file addr1.payment.sk.json  \
--signing-key-file policy/policy.sk.json  \
$CARDANO_TESTNET --tx-body-file mint.raw  \
--out-file mint.signed
```

Submit the tx
```
cardano-cli transaction submit --tx-file mint.signed $CARDANO_TESTNET
```

Check mint status
```
cardano-cli query utxo --address $ADDRESS_1 $CARDANO_TESTNET
```