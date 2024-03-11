# Guide for Side Protocol Node
## Hardware Specifications
###Minimum Requirements
```
CPU: 4 cores
RAM: 8 GB
Storage: 200 GB
Network: 1 Gbps
Recommended Specifications
CPU: 8 cores
RAM: 16 GB
Storage: 500 GB
Network: 1 Gbps
```
# Install Go
## Install Go
```bash
cd $HOME
curl https://dl.google.com/go/go1.20.5.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -

# Update environment variables to include go
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF

source $HOME/.profile

# check go version
go version
```

## Install the Node
```bash
# Install Sidechain Testnet node
mkdir -p $HOME/go/bin
git clone https://github.com/sideprotocol/sidechain.git side
cd side
git checkout v0.6.0
make install
```

## Initialize the Node
```bash
# Initialize Node
sided init <moniker> --chain-id=side-testnet-2

# Example:
sided init Synergy_Nodes --chain-id=side-testnet-2
```

## Download Genesis file
```bash
curl -Ls https://snapshots.synergynodes.com/genesis/side_testnet/genesis.json > $HOME/.side/config/genesis.json
```

## Download Addrbook file
```bash
curl -Ls https://snapshots.synergynodes.com/addrbook/side_testnet/addrbook.json > $HOME/.side/config/addrbook.json
```

## Add / Update Persistent Peers
```bash
# Add / Update Peers
PEERS=86145a563c0178349247d3e239b85d57d99e1993@65.108.41.177:34656,09ea56a225a4c3e525ae4cfc1311157dbcbe33c9@141.95.65.26:24966,b32942a7382aa2433d5d3b068c9b5ce9cb94e358@148.251.11.99:11356,25101be6dbaae1201fff5ebd213afb1dfc675d90@37.60.239.244:26656,5b21074ff383280912042e610c41c33526a0a616@185.119.116.238:26656,00e0d0a39d56bb945de4d4179ae0bd49679cac65@65.108.206.118:46656,bbbf623474e377664673bde3256fc35a36ba0df1@65.109.36.231:45656,cb0c64e85255d127d5e9c16f7db9c995b3600c37@152.228.208.164:26656,5e0b5f26e4c069fbcaac1ae4b22aba151e463a52@65.108.79.241:60856,e9d50c5af9ea9e14b11bae091b75f3e3bbc25de7@65.109.124.51:11356
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = "$PEERS"/" $HOME/.side/config/config.toml
```

## Download & decompress Snapshot
```bash
# Download the snapshot
wget -O side_81897.tar.lz4 https://snapshots.synergynodes.com/side_testnet/side_81897.tar.lz4

# Decompress the snapshot
lz4 -c -d side_81897.tar.lz4 | tar -x -C $HOME/.side
```

## Create Service File
```bash
# Create Service
sudo tee /etc/systemd/system/sided.service > /dev/null <<EOF
[Unit]
Description=sided Daemon
#After=network.target
StartLimitInterval=350
StartLimitBurst=10

[Service]
Type=simple
User=node
ExecStart=/home/node/go/bin/sided start
Restart=on-abort
RestartSec=30

[Install]
WantedBy=multi-user.target

[Service]
LimitNOFILE=1048576
EOF
```

## Start the Node

# Enable service
```
sudo systemctl enable sided
```

# Start service
```
sudo service sided start
```

# Check logs
```
sudo journalctl -fu sided
```

