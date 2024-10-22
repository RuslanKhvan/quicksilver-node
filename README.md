## Quicksilver Testnet Node Installation Guide

### 1. **System Requirements**

Before starting, ensure your server meets the following minimum requirements:

- **Operating System**: Ubuntu 20.04 or higher
- **Memory**: 8GB RAM
- **Storage**: 250GB SSD
- **CPU**: 4 cores
- **Bandwidth**: High-speed internet connection

### 2. **Update and Install Required Dependencies**

Update the package lists and install essential packages.

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential git wget jq make gcc -y
```

### 3. **Install Go**

Quicksilver requires Go to build from the source. Install Go as follows:

```bash
wget https://golang.org/dl/go1.20.5.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.20.5.linux-amd64.tar.gz
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bashrc
source ~/.bashrc
```

Verify Go installation:

```bash
go version
```

### 4. **Clone the Quicksilver Repository**

Download the Quicksilver source code and checkout the latest testnet branch:

```bash
git clone https://github.com/ingenuity-build/quicksilver.git
cd quicksilver
git checkout v1.4.0
make build
```

Move the binary to your executable path:

```bash
sudo mv ./build/quicksilverd /usr/local/bin/
```

Verify the installation by checking the version:

```bash
quicksilverd version
```

### 5. **Initialize the Node**

Set up the configuration for your node:

```bash
quicksilverd init <YOUR_MONIKER> --chain-id quicksilver-testnet-12
```

Replace `<YOUR_MONIKER>` with your desired node name.

### 6. **Download the Genesis File**

Fetch the genesis file required for the testnet:

```bash
wget -O ~/.quicksilverd/config/genesis.json "https://raw.githubusercontent.com/ingenuity-build/testnets/main/quicksilver-testnet-12/genesis.json"
```

### 7. **Set Up Peers and Seeds**

Add peers and seeds to the configuration file. Open `config.toml`:

```bash
nano ~/.quicksilverd/config/config.toml
```

Locate the `persistent_peers` and `seeds` sections, then add the following:

```bash
persistent_peers = "PEER_LIST_HERE"
seeds = "SEED_LIST_HERE"
```

Make sure to replace `PEER_LIST_HERE` and `SEED_LIST_HERE` with actual peer and seed addresses. You can find these from community forums or explorers.

### 8. **Configure Pruning and Minimum Gas Prices**

To optimize disk usage and set the minimum gas price, edit the `app.toml`:

```bash
nano ~/.quicksilverd/config/app.toml
```

Set pruning:

```bash
pruning = "custom"
pruning-keep-recent = "100"
pruning-interval = "10"
```

Set the minimum gas price (recommended: `0.0001uqck`):

```bash
minimum-gas-prices = "0.0001uqck"
```

### 9. **Configure State Sync (Optional)**

If you want to use state sync, edit `config.toml`:

```bash
nano ~/.quicksilverd/config/config.toml
```

Uncomment and update the following lines:

```bash
enable = true
rpc_servers = "<STATE_SYNC_RPC_SERVERS>"
trust_height = <TRUST_HEIGHT>
trust_hash = "<TRUST_HASH>"
```

Replace placeholders with correct values from a trusted state sync provider.

### 10. **Start the Node**

You are now ready to start your Quicksilver node:

```bash
quicksilverd start
```

To run the node in the background, you can use `systemd`.

### 11. **Create a Systemd Service**

Create a systemd service file:

```bash
sudo tee /etc/systemd/system/quicksilverd.service > /dev/null <<EOF
[Unit]
Description=Quicksilver Node
After=network-online.target

[Service]
User=$USER
ExecStart=/usr/local/bin/quicksilverd start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

Reload the systemd daemon, enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable quicksilverd
sudo systemctl start quicksilverd
```

Check logs to ensure everything is working:

```bash
sudo journalctl -u quicksilverd -f
```

### 12. **Create a Wallet**

Create a new wallet by running:

```bash
quicksilverd keys add <WALLET_NAME>
```

Make sure to securely store the generated mnemonic.

### 13. **Request Testnet Tokens**

Once your node is running, you can request testnet tokens from a faucet or by following instructions in the community channels.
