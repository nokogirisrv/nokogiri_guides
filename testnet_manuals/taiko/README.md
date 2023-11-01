# Guide for Taiko proposer testnet
## Setting your your server
```
apt install git
```
```
sudo apt update
```
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```
```
apt-cache policy docker-ce
```
```
sudo apt install docker-ce
```
## Node setup
```
git clone https://github.com/taikoxyz/simple-taiko-node.git
```
```
cd simple-taiko-node
```
```
cp .env.sample .env
```
```
nano .env
```
Go to [Alchemy](https://alchemy.com/?r=baa923610b5f9fe0) and [register](https://alchemy.com/?r=baa923610b5f9fe0). In this section, click on + Create new app.
1) Choose Chain as Ethereum, and select Ethereum Sepolia as the Network. Create.
2) Navigate to your API key and copy the HTTPS key. Fill out it on L1_ENDPOINT_HTTP=
![image](https://github.com/Nokogirisrv/nokogiri_guides/assets/125523696/b3c11ae9-9fdc-4145-ab07-41a0ebd3b856)
4) Navigate back to Alchemy and now copy the line under WebSocket. Paste it on L1_ENPOINTS_WS=:
![image](https://github.com/Nokogirisrv/nokogiri_guides/assets/125523696/196529a6-7bf8-4925-b80c-6b492a81a615)
## Run the node
```
docker compose up -d
```
## Check the node with: 
http://SERVER_IP:3001/d/L2ExecutionEngine/l2-execution-engine-overview?orgId=1&refresh=10s (Replace SERVER_IP with your server IP)
## Wait until our node is fully synced
http://dojonode.xyz/#/dashboard, click on the satellite dish and paste the server's IP in the first line instead of localhost.
![image](https://github.com/Nokogirisrv/nokogiri_guides/assets/125523696/b46f2f0d-5162-4835-af81-15a780e7db81)

## If you run proposer:
Use [faucets](https://taiko.xyz/docs/guides/receive-tokens) to get SeopliaETH
1) Open .env file 
```
cd simple-taiko-node
```
```
docker compose down
```
```
nano .env
```

2) Fill out with your Private_key L1_PROPOSER_PRIVATE_KEY=

3) Fill out with your address L2_SUGGESTED_FEE_RECIPIENT=

4) Paste the following immediately after PROVER_ENDPOINTS=
```
http://jolnir.spachib.xyz:9876,http://65.21.14.11:9876,https://taiko-a5-prover.zkpool.io,http://pool-1.taikopool.xyz,http://taiko.web3cript.xyz:9876,http://ttko.web3cript.xyz:9876,http://purethereal.xyz:9876,http://karmanodes.xyz,http://65.21.90.249:9876,http://158.220.89.198:9876,http://62.183.54.219:9876,http://45.144.28.60:9876,http://185.173.38.221:9876,http://178.254.38.174:9876,http://161.97.133.1:9876,http://161.97.146.121:9876,http://161.97.160.96:9876
```
4) Change the flag for  ENABLE_PROPOSER=false to true
5) Restart the docker
```
docker compose up -d
```
## Useful commands
For checking proposer logs:
```
docker compose logs -f taiko_client_proposer
```
For checking proposer succeeded transactions:
```
docker compose logs -f taiko_client_proposer | egrep "Propose transactions succeeded”
```
