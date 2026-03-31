<div align="center">
  <h1>🚀 Oxin Chain (Testnet)</h1>
  <p><b>The Official Node Setup, Mining, Reward and Network Guide</b></p>
  <p>Secure the network, run a node, and earn early-adopter block rewards.</p>
</div>

---

## 📖 Table of Contents
- [Network Details](#-network-details)
- [Node Setup & Mining Guide](#-node-setup--mining-guide)
  - [Phase 1: Server Requirements](#phase-1-server-requirements)
  - [Phase 2: Install Prerequisites](#phase-2-install-prerequisites-docker)
  - [Phase 3: Create Miner Wallet](#phase-3-create-miner-wallet)
  - [Phase 4: Genesis Initialization](#phase-4-download-genesis--initialize-database)
  - [Phase 5: Start Mining](#phase-5-start-mining)
  - [Phase 6: Claim Rewards in MetaMask](#phase-6-claim-rewards-in-metamask)
- [Mining Economics & Halving](#-mining-economics--halving)
- [🛠️ Advanced Troubleshooting (A to Z)](#️-advanced-troubleshooting-a-to-z)

---

## 🌐 Network Details

Add Oxin Chain to your Web3 wallet using the following details:

| Configuration | Value |
| :--- | :--- |
| **Network Name** | Oxin Testnet |
| **New RPC URL** | `https://testnet-rpc.oxinchain.io` |
| **Chain ID** | `4456` |
| **Currency Symbol** | `OXIN` |
| **Block Explorer** | [https://testnet-scan.oxinchain.io](https://testnet-scan.oxinchain.io) |
| **Consensus Engine** | PoA (Proof of Authority - Clique) |
| **Block Time** | 5 Seconds |

---

## 🖥️ Node Setup & Mining Guide

By running a node, you help secure our fast, 5-second PoA network. In return, your node earns **OXIN** testnet tokens through block rewards and transaction fees. No advanced coding skills are required to get started.

### Phase 1: Server Requirements
A lightweight Virtual Private Server (VPS) is highly recommended for 24/7 uptime.

* **Minimum Specs:** 2 Core CPU, 4 GB RAM, 50 GB SSD/NVMe
* **OS:** Ubuntu 22.04 LTS or 24.04 LTS
* **Providers:** Contabo, DigitalOcean, Hetzner, Vultr, etc. (~$5-$10/month)

Connect to your server via SSH:
```bash
ssh root@YOUR_SERVER_IP 


Phase 2: Install Prerequisites (Docker)
We use Docker to ensure smooth, isolated, and crash-free node operations. Run these commands to install Docker:

Bash
sudo apt update && sudo apt upgrade -y
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
Phase 3: Create Miner Wallet
You need a local wallet on your server to collect block rewards.

1. Create a data directory:

Bash
mkdir -p ~/oxin-node/data
2. Create a secure password file:

⚠️ IMPORTANT: Replace YourStrongPasswordHere with your own password. Do not lose this!

Bash
echo "YourStrongPasswordHere" > ~/oxin-node/data/password.txt
3. Generate the wallet:

Bash
docker run --rm -v ~/oxin-node/data:/data ethereum/client-go:v1.13.15 account new --datadir /data --password /data/password.txt
✅ SAVE YOUR ADDRESS: The terminal will output Public address of the key: 0x.... Copy this 0x... address safely. This is your Miner Address.

Phase 4: Download Genesis & Initialize Database
The genesis.json file contains the core rules of the Oxin Chain.

1. Download the Genesis file:

Bash
curl -o ~/oxin-node/genesis.json [https://raw.githubusercontent.com/oxinchain/oxin-testnet/refs/heads/main/genesis.json](https://raw.githubusercontent.com/oxinchain/oxin-testnet/refs/heads/main/genesis.json)
2. Initialize the database:

Bash
docker run --rm -v ~/oxin-node/data:/data -v ~/oxin-node/genesis.json:/genesis.json ethereum/client-go:v1.13.15 --datadir /data init /genesis.json
Phase 5: Start Mining!
Connect your node to the network and start sealing blocks.

⚠️ ATTENTION: Replace YOUR_MINER_ADDRESS_HERE (in TWO places) with your 0x... address from Phase 3. Paste the entire block below as ONE single line in your terminal.

Bash
docker run -d --name oxin-miner --restart always -v ~/oxin-node/data:/data -p 8546:8546 -p 30304:30304 -p 30304:30304/udp ethereum/client-go:v1.13.15 --networkid 4456 --datadir /data --syncmode full --snapshot=false --port 30304 --http --http.addr 0.0.0.0 --http.port 8546 --http.api eth,net,web3,miner,clique,admin --http.corsdomain "*" --http.vhosts "*" --mine --miner.etherbase "YOUR_MINER_ADDRESS_HERE" --unlock "YOUR_MINER_ADDRESS_HERE" --password /data/password.txt --allow-insecure-unlock --bootnodes "enode://98f50b704955035d5a2593cc9a1a187ca6a7e022a9e5fc7166a6e1fb05cc591e598786c80827b7628c12190d9a1f5a539c63da6c58e192fee98bf6a1b0d7a3e8@185.209.223.111:30304"
Verify your node is running and syncing:

Bash
docker exec -it oxin-miner geth attach --exec "eth.blockNumber" /data/geth.ipc
(If the number increases over time, your node is successfully synced!)

Phase 6: Claim Rewards in MetaMask
To spend your OXIN, import your node's wallet into MetaMask.

1. Locate your Keystore File:

Bash
sudo ls -l ~/oxin-node/data/keystore
Copy the filename starting with UTC--...

2. Read the Keystore Data:

Bash
sudo cat ~/oxin-node/data/keystore/YOUR_FILE_NAME
Copy the entire output from {"address":... to the end ..."version":3}. Save it to your PC as a file named oxin-wallet.json (Save as "All Files", not .txt).

3. Import to MetaMask:

Add the Oxin Testnet to MetaMask using the Network Details at the top of this guide.

Click Add account or hardware wallet -> Import account.

Select JSON File, upload oxin-wallet.json, and enter your password.

You will now see your live OXIN balance!

💰 Mining Economics & Halving
Oxin Chain is designed with a deflationary halving mechanism:

Block Rewards: Earn OXIN and transaction gas fees for every 5-second block your node helps seal.

The Halving Cycle: Similar to Bitcoin, Oxin Chain employs a "Halving" schedule. After a specific number of blocks, the mining reward is cut in half.

Early Adopter Advantage: Run your node 24/7 now to maximize your holdings during the highest emission phase before the next halving!

🛠️ Advanced Troubleshooting (A to Z)
If your node faces issues, use these commands to diagnose and fix the problem.

1. Check Node Health & Logs
To see what your node is doing in real-time:

Bash
docker logs --tail 50 -f oxin-miner
Press Ctrl + C to exit the logs.

2. Check Peer Connections
If your block number isn't increasing, check if you are connected to the network:

Bash
docker exec -it oxin-miner geth attach --exec "net.peerCount" /data/geth.ipc
If the output is 0, you are disconnected. See Fix #1 below.

3. Common Issues & Fixes
Fix #1: Node is disconnected (peercount = 0 / Network out of sync)
This happens if your server restarts abruptly or runs out of RAM. You need to reset the container.

Bash
# Stop and remove the stuck container
docker stop oxin-miner && docker rm oxin-miner

# Restart the node using your Phase 5 Run Command
Fix #2: Database Corruption (Hard Reset)
If your node is stuck on a wrong block hash or refuses to start, you must wipe the local chain data and re-sync from block 0. (This will NOT delete your wallet).

Bash
# 1. Stop the node
docker stop oxin-miner && docker rm oxin-miner

# 2. Delete corrupted chain data (Keep keystore safe)
sudo rm -rf ~/oxin-node/data/geth

# 3. Re-initialize Genesis
docker run --rm -v ~/oxin-node/data:/data -v ~/oxin-node/genesis.json:/genesis.json ethereum/client-go:v1.13.15 --datadir /data init /genesis.json

# 4. Restart the node using your Phase 5 Run Command
Fix #3: "Snapshot Failed" Error
Our network uses --snapshot=false and --syncmode full in the run command. Ensure you did not remove these tags from your docker run command, as generating snapshots on high-traffic nodes can cause memory crashes.

<div align="center">
<b>Welcome to the future. Welcome to Oxin Chain. 🌐💎</b>
</div>
