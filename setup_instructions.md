# Cardano Node and Cardano CLI

## Pre-requisites

#Installing Ubuntu VM (assuming the environment is already setup

**Source:** https://ubuntu.com/tutorials/how-to-run-ubuntu-desktop-on-a-virtual-machine-using-virtualbox#1-overview
Switch to expert mode during intial config to allocate : 4 Processors/cpu cores
100 GB of file VDI file size.
8-10 GM RAM

This document is my summary of:  
- https://iohk.zendesk.com/hc/en-us/articles/900001951646-Building-a-node-from-source  
- https://github.com/mallapurbharat/cardano-tx-sample/blob/main/exercise1-simpletxfer.md  
- https://github.com/mallapurbharat/cardano-tx-sample/blob/main/multutxo/multiutxo.md  
## Update and Install OS Dependencies
```bash
sudo apt-get update -y
sudo apt-get install automake build-essential curl pkg-config libffi-dev libgmp-dev libssl-dev libtinfo-dev libsystemd-dev zlib1g-dev make g++ tmux git jq wget libncursesw5 libtool autoconf
```
## Install GHCup
```bash
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```
## Setup GHC and Cabal (a version is installed by default, check and change if required)
```bash
ghcup install ghc 8.10.7
ghcup set ghc 8.10.7
gch --version
```
```bash
ghcup install cabal 3.6.2.0
ghcup set cabal 3.6.2.0
cabal --version
```
## Build Cardano
```bash
mkdir ~/cardano-src
```
### libsodium
```bash
cd ~/cardano-src
git clone https://github.com/input-output-hk/libsodium 
cd ~/cardano-src/libsodium 
git checkout 66f017f1 
./autogen.sh
./configure 
make 
sudo make install
```
### secp256k1
```bash
cd ~/cardano-src
git clone https://github.com/bitcoin-core/secp256k1
cd ~/cardano-src/secp256k1
git checkout ac83be33
./autogen.sh
./configure --enable-module-schnorrsig --enable-experimental
make
sudo make install
```
### Library Paths
Append these exports to your `~/.bashrc` file.  
```bash
export LD_LIBRARY_PATH="/usr/local/lib${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}"
export PKG_CONFIG_PATH="/usr/local/lib/pkgconfig${PKG_CONFIG_PATH:+:$PKG_CONFIG_PATH}"
```
Logout and login again.  
### cardano-node/cli
```bash
cd ~/cardano-src
git clone https://github.com/input-output-hk/cardano-node.git
cd ~/cardano-src/cardano-node
git fetch --all --recurse-submodules --tags
git checkout $(curl -s https://api.github.com/repos/input-output-hk/cardano-node/releases/latest | jq -r .tag_name)
cabal configure --with-compiler=ghc-8.10.7
cabal build all
```
### Run from Anywhere
```bash
cd ~/cardano-src/cardano-node
mkdir -p ~/.local/bin
rm -f ~/.local/bin/cardano-node
rm -f ~/.local/bin/cardano-cli
ln -s "$(./scripts/bin-path.sh cardano-node)" ~/.local/bin/cardano-node
ln -s "$(./scripts/bin-path.sh cardano-cli)" ~/.local/bin/cardano-cli
cardano-cli --version
cardano-node --version
```
> Versions should be equal to the version/tag (e.g. 1.35.0) that was built previously.  
## Run Cardano Node on testnet
Append these exports to your `~/.bashrc` file.  
```bash
export CARDANO_HOME="${HOME}/cardano-src/cardano-node"
export CARDANO_CONFIG="${CARDANO_HOME}/configuration/cardano"
export CARDANO_DB_TESTNET="${CARDANO_HOME}/db-test"
export CARDANO_NODE_SOCKET_PATH="${CARDANO_DB_TESTNET}/node.socket"
export CARDANO_TESTNET='--testnet-magic=1097911063'
```
Logout and login again.  
```bash
cardano-node run \
    --topology="${CARDANO_CONFIG}/testnet-topology.json" \
    --database-path="${CARDANO_DB_TESTNET}" \
    --socket-path="${CARDANO_NODE_SOCKET_PATH}" \
    --port=3001 \
    --config="${CARDANO_CONFIG}/testnet-config.json"
```
> Sync with testnet will take hours!  
> Use `ctrl-c` to stop it.  
> Next time you run this command, it will continue syncing, from where you left before.  
## Check Synchronization Progress
While sync is running, run this command on another terminal window.  
```bash
cardano-cli query tip ${CARDANO_TESTNET}
```
It returns a JSON object like the one below. 
```json
{
    "era": "Alonzo", 
    "syncProgress": "100.00",
    "hash": "5bb51f52da93020adefe04bb8447a394ab6670528b62d9bb01649d62d9538da8",
    "epoch": 212,
    "slot": 61276634,
    "block": 3643388
}
```
> Pay attention to the `syncProgress` attribute.  
> It indicates the percentage of the synchronization process.  
> Only when `100.00` it means that your copy of the testnet blockchain is up-to-date.  
## Saving Time with Bash Completion
Append these bash-completion commands to your `~/.bashrc` file.  
```bash
source <(cardano-node --bash-completion-script `which cardano-node`)
source <(cardano-cli  --bash-completion-script `which cardano-cli`)
```
Also an alias can be added to run node easily
```
alias runCnode='cardano-node run --topology="${CARDANO_CONFIG}/testnet-topology.json" --database-path="${CARDANO_DB_TESTNET}" --socket-path="${CARDANO_NODE_SOCKET_PATH}" --port=3001 --config="${CARDANO_CONFIG}/testnet-config.json"'
```
Logout and login again.  
