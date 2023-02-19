<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/169664551-39020c2e-fa95-483b-916b-c52ce4cb907c.png">
</p>

# Chain upgrade to 1.2.2beta
## (OPTION 1) Manual upgrade
Once the chain reaches the upgrade height, you will encounter the following panic error message:\
`ERR UPGRADE "xxx" NEEDED at height: 8871251`
```
cd $HOME && rm $HOME/sei-chain -rf
git clone https://github.com/sei-protocol/sei-chain.git && cd $HOME/sei-chain
git checkout 1.2.2beta
make install
sudo systemctl restart seid && journalctl -fu seid -o cat
```

!!! DO NOT UPGRADE BEFORE CHAIN RECHES THE BLOCK `8871251`!!!

### (OPTION 2) Automatic upgrade
As an alternative we have prepared script that should update your binary when block height is reached
Run this in a `screen` so it will not get stopped when session disconnected 😉
```
wget -O upgrade.sh https://raw.githubusercontent.com/kj89/testnet_manuals/main/sei/sei-devnet-1/upgrade/8871251/upgrade.sh && chmod +x upgrade.sh && ./upgrade.sh
```
