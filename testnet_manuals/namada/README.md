# Guide for Namada node

## Here is a node installation guide (Simplified and manual)

### Preparing the server for installation
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config git make libssl-dev libclang-dev libclang-12-dev -y
sudo apt install jq build-essential bsdmainutils ncdu gcc git-core chrony liblz4-tool -y
sudo apt install uidmap dbus-user-session protobuf-compiler unzip -y
```
### Installing Rust and NodeJS
```
cd $HOME
sudo apt update
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
. $HOME/.cargo/env
curl https://deb.nodesource.com/setup_18.x | sudo bash
sudo apt install cargo nodejs -y < "/dev/null"
cargo --version
node -v
```
### Installing Go
```
ver="1.19.4"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version
```
### Installing Proto
```
cd $HOME && rustup update
PROTOC_ZIP=protoc-23.3-linux-x86_64.zip
curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v23.3/$PROTOC_ZIP
sudo unzip -o $PROTOC_ZIP -d /usr/local bin/protoc
sudo unzip -o $PROTOC_ZIP -d /usr/local 'include/*'
rm -f $PROTOC_ZIP
protoc --version
```
### Check your variables in /.bash_profile and modify them if they are incorrect.
```
sed -i '/public-testnet/d' "$HOME/.bash_profile"
sed -i '/NAMADA_TAG/d' "$HOME/.bash_profile"
sed -i '/WALLET_ADDRESS/d' "$HOME/.bash_profile"
sed -i '/CBFT/d' "$HOME/.bash_profile"
```
### Setting up the variables
```
echo "export NAMADA_TAG=v0.23.1" >> ~/.bash_profile
echo "export CBFT=v0.37.2" >> ~/.bash_profile
echo "export CHAIN_ID=public-testnet-14.5d79b6958580" >> ~/.bash_profile
echo "export WALLET=wallet" >> ~/.bash_profile
echo "export BASE_DIR=$HOME/.local/share/namada" >> ~/.bash_profile
### CHANGE the validator's name!
```
```
echo "export VALIDATOR_ALIAS=<YOUR_MONIKER>" >> ~/.bash_profile
source ~/.bash_profile
```
### Build
```
cd $HOME && git clone https://github.com/anoma/namada && cd namada && git checkout $NAMADA_TAG
make build-release
```
### Build TBFT
```
cd $HOME && git clone https://github.com/cometbft/cometbft.git && cd cometbft && git checkout $CBFT
make build
```
### Moving binary files to the necessary directory.
```
cd $HOME && cp $HOME/cometbft/build/cometbft /usr/local/bin/cometbft && \
cp "$HOME/namada/target/release/namada" /usr/local/bin/namada && \
cp "$HOME/namada/target/release/namadac" /usr/local/bin/namadac && \
cp "$HOME/namada/target/release/namadan" /usr/local/bin/namadan && \
cp "$HOME/namada/target/release/namadaw" /usr/local/bin/namadaw
cometbft version
namada --version
```
### Create a service file
```
sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
```
```
[Unit]
Description=namada
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.local/share/namada
Environment=TM_LOG_LEVEL=p2p:none,pex:error
Environment=NAMADA_CMT_STDOUT=true
ExecStart=/usr/local/bin/namada node ledger run 
StandardOutput=syslog
StandardError=syslog
Restart=always
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable namadad
```
### Start your node
```
cd $HOME && namada client utils join-network --chain-id $CHAIN_ID
sudo systemctl start namadad && sudo journalctl -u namadad -f -o cat
```
## Wait for full synchronization
```
curl -s localhost:26657/status
```
# It may display incorrectly and return an error "false." You can check the block height here: ​
### Wallet creation
```
cd $HOME
namada wallet address gen --alias $WALLET --unsafe-dont-encrypt
namada client transfer \
  --source faucet \
  --target $WALLET \
  --token NAM \
  --amount 1000 \
  --signer $WALLET
```
### Validator initialization
```
cd $HOME
namada client init-validator \
--alias $VALIDATOR_ALIAS \
--source $WALLET \
--commission-rate 0.05 \
--max-commission-rate-change 0.01 \
--signer $WALLET \
--gas-amount 100000000 \
--gas-token NAM \
--unsafe-dont-encrypt
```
### Requesting tokens from a faucet 1'st time
```
cd $HOME
namada client transfer \
--token NAM \
--amount 1000 \
--source faucet \
--target $VALIDATOR_ALIAS \
--signer $VALIDATOR_ALIAS
```
### Requesting tokens from a faucet 2'nd time
```
namada client transfer \
--token NAM \
--amount 1000 \
--source faucet \
--target $VALIDATOR_ALIAS \
--signer $VALIDATOR_ALIAS
```
### Checking the balance
```
namada client balance --owner $VALIDATOR_ALIAS --token NAM
```
## Checking the epoch
```
namada client epoch
```
### Stake your tokens in the validator.
# You need to wait for 2 epochs and continue. If you receive the "INFO atest1..." does not belong to any known validator account.
namada client bond \
--validator $VALIDATOR_ALIAS \
--amount 1888 \
--signer $VALIDATOR_ALIAS \
--source $VALIDATOR_ALIAS
Display your validator and wallet addresses
bash
RAW_ADDRESS=`cat "$HOME/.local/share/namada/$CHAIN_ID/wallet.toml" | grep "address ="`
WALLET_ADDRESS=$(echo -
