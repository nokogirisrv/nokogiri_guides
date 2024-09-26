# Guide for NESA miner testnet setup
## Install GPU Drivers

```bash
sudo apt-get install linux-headers-$(uname -r)

distribution=$(. /etc/os-release;echo $ID$VERSION_ID | sed -e 's/\.//g')

wget https://developer.download.nvidia.com/compute/cuda/repos/$distribution/x86_64/cuda-keyring_1.0-1_all.deb

sudo dpkg -i cuda-keyring_1.0-1_all.deb

sudo apt-get update

sudo apt-get -y install cuda-drivers
```

---

```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list

sudo apt-get update

sudo apt-get install -y nvidia-container-toolkit
```

---

```bash
sudo nvidia-ctk runtime configure --runtime=docker

sudo systemctl restart docker

nvidia-smi
#nvcc --version
```

---

## Obtaining Hugging Face API Token
1. Go to https://huggingface.co/docs/hub/security-tokens and register.
2. Confirm your email after registering.
3. Navigate to account settings and select **Access Tokens**.
4. Click on **Create New Token**, assign permissions, and save the token securely!
![image](https://github.com/user-attachments/assets/81b116f7-5c58-41ac-a492-6cfb0ad44477)

Multiple VPS instances can use the same Hugging Face API token to access the API, within usage limits defined by your plan.

---

## Start Mining

To start mining, use the commands below:

- If running as root:
  ```bash
  curl -sSL https://raw.githubusercontent.com/nesaorg/bootstrap/master/bootstrap.sh | sudo bash
  ```

- Universal script for running a validator or miner:
  ```bash
  bash <(curl -s https://raw.githubusercontent.com/nesaorg/bootstrap/master/bootstrap.sh)
  ```

Follow the Wizardry instructions, set up your moniker, email, referral wallet, and enter the Hugging Face API key. A Cosmos-compatible private key is also required.

Between Wizardry and Advanced Wizardry. Select Wizardry
![image](https://github.com/user-attachments/assets/b28eff62-8e97-4fc4-8fba-d9d79483253d)

Type your moniker and press ENTER
![image](https://github.com/user-attachments/assets/fa3fcf97-ed38-4b79-a377-925fc595f3b6)

Type your email and press ENTER
![image](https://github.com/user-attachments/assets/019404fa-85cd-4173-9770-a7b7086b579e)

Type your referral address(if you want to use ours: nesa1xytdt4shkjgxa2e9zlnddhzk0z7hz0uuz2qa40 )
![image](https://github.com/user-attachments/assets/cc5ad113-acfa-4ae9-b210-81a5b3db3905)

Type your Huggingface API key and press ENTER
![image](https://github.com/user-attachments/assets/e9b45789-5556-49e1-8e4d-330c2bb10100)



To check your miner stats:
```bash
node_id=$(cat $HOME/.nesa/identity/peer_id.id)
echo https://node.nesa.ai/nodes/$node_id
```

---

## Useful Commands
- Check logs:
  ```bash
  docker logs -f -n 100 orchestrator
  docker logs -f -n 100 ipfs_node
  docker logs -f -n 100 mongodb
  ```

- Restart containers:
  ```bash
  docker restart orchestrator mongodb docker-watchtower-1 ipfs_node
  ```

- Stop containers:
  ```bash
  docker stop orchestrator mongodb docker-watchtower-1 ipfs_node
  ```

- Remove containers:
  ```bash
  docker rm orchestrator mongodb docker-watchtower-1 ipfs_node
  ```
```
