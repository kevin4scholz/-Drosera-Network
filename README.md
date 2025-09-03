# -Drosera-Network
In this Guide, we contribute to Drosera testnet by:

Installing the CLI
Setting up a vulnerable contract
Deploying a Trap on testnet
Connecting an operator to the Trap
Recommended System Requirements
2 CPU Cores
4 GB RAM
20 GB Disk Space
Official Discord

Install Dependecies
sudo apt-get update && sudo apt-get upgrade -y
sudo apt install curl ufw iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
Docker:

sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt upgrade -y

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Test Docker
sudo docker run hello-world
Trap Setup
1. Configure Enviorments
Drosera CLI:

curl -L https://app.drosera.io/install | bash
source /root/.bashrc
droseraup
Foundry CLI:

curl -L https://foundry.paradigm.xyz | bash
source /root/.bashrc
foundryup
Bun:

curl -fsSL https://bun.sh/install | bash
2. Deploy Contract & Trap
mkdir my-drosera-trap
cd my-drosera-trap
Replace Github_Email & Github_Username:

git config --global user.email "Github_Email"
git config --global user.name "Github_Username"
Initialize Trap:

forge init -t drosera-network/trap-foundry-template
Compile Trap:

curl -fsSL https://bun.sh/install | bash
bun install
forge build
skip warnings!

Deploy Trap:

DROSERA_PRIVATE_KEY=xxx drosera apply
Replace xxx with your EVM wallet privatekey (Ensure it's funded with Holesky ETH)
Enter the commamd, when prompted, write ofc and press Enter.
image

3. Check Trap in Dashboard
1- Connect your Drosera EVM wallet: https://app.drosera.io/

2- Click on Traps Owned to see your deployed Traps OR search your Trap address.

image

4. Bloom Boost Trap
Open your Trap on Dashboard and Click on Send Bloom Boost and deposit some Holesky ETH on it.

image

5. Fetch Blocks
drosera dryrun
Operator Setup
1. Whitelist Your Operator
1- Edit Trap configuration:

cd my-drosera-trap
nano drosera.toml
Add the following codes at the bottom of drosera.toml:

private_trap = true
whitelist = ["Operator_Address"]
Replace Operator_Address with your EVM wallet Public Address between " " symbols
Your Public Address is your Operator_Address.
2- Update Trap Configuration:

DROSERA_PRIVATE_KEY=xxx drosera apply
Replace xxx with your EVM wallet privatekey
Your Trap should be private now with your operator address whitelisted internally.

2. Operator CLI
cd ~
# Download
curl -LO https://github.com/drosera-network/releases/releases/download/v1.16.2/drosera-operator-v1.16.2-x86_64-unknown-linux-gnu.tar.gz

# Install
tar -xvf drosera-operator-v1.16.2-x86_64-unknown-linux-gnu.tar.gz
Test the CLI with ./drosera-operator --version to verify it's working.

# Check version
./drosera-operator --version

# Move path to run it globally
sudo cp drosera-operator /usr/bin

# Check if it is working
drosera-operator
3. Install Docker image
docker pull ghcr.io/drosera-network/drosera-operator:latest
4. Register Operator
drosera-operator register --eth-rpc-url https://ethereum-holesky-rpc.publicnode.com --eth-private-key PV_KEY
Replace PV_KEY with your Drosera EVM privatekey. We use the same wallet as our trap wallet.
5. Open Ports
# Enable firewall
sudo ufw allow ssh
sudo ufw allow 22
sudo ufw enable

# Allow Drosera ports
sudo ufw allow 31313/tcp
sudo ufw allow 31314/tcp
6. Install & Run Operator
Choose one Installation Method:

Method 1: Install using Docker
Method 2: Install using SystemD
Method 1: Docker
6-1-1: Configure Docker
Make sure you have installed Docker in Dependecies step.
If you are currently running via old systemd method, stop it:

sudo systemctl stop drosera
sudo systemctl disable drosera
git clone https://github.com/0xmoei/Drosera-Network
cd Drosera-Network
cp .env.example .env
Edit .env file.

nano .env
Replace your_evm_private_key and your_vps_public_ip
6-1-2: Run Operator
docker compose up -d
6-1-3: Check health
cd Drosera-Network
docker compose logs -f
No problem if you are receiveing WARN drosera_services::network::service: Failed to gossip message: InsufficientPeers

6-1-4: Optional Docker commands
# Stop node
cd Drosera-Network
docker compose down -v

# Restart node
cd Drosera-Network
docker compose up -d
Now running your node using Docker, you can Jump to step 7.

Method 2: SystemD
6-2-1: Configure SystemD service file
Enter this command in the terminal, But first replace:

PV_KEY with your privatekey
VPS_IP with your solid vps IP (without anything else)
if using a local system, then replace vps ip with 0.0.0.0
sudo tee /etc/systemd/system/drosera.service > /dev/null <<EOF
[Unit]

  
