# AIRCHAINS

Website: https://www.airchains.io/

Twitter: https://twitter.com/airchains_io

Discord: https://discord.com/invite/airchains

System Requirements (Recommended)

CPU: 2 core

RAM: 8 GB

Storage(SSD): 200 GB SSD or Nvme

**Install required packages**
```
sudo apt update && sudo apt upgrade -y
sudo apt install make curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

**Install Go**
```
cd $HOME && \
ver="1.22.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

**Build binary**
```
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
sudo mv junctiond /usr/local/bin
```

**Initialize the Node with the Moniker**
```
junctiond init <moniker>
```

**Download Genesis**
```
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/genesis.json
cp genesis.json $HOME/.junction/config/genesis.json
```

**Configure**
```
SEEDS="2d1ea4833843cc1433e3c44e69e297f357d2d8bd@5.78.118.106:26656"
PEERS="de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.junction/config/config.toml
```

**Set Pruning, Enable Prometheus, Gas Price, and Indexer**
```
PRUNING="custom"
PRUNING_KEEP_RECENT="100"
PRUNING_INTERVAL="19"
sed -i -e "s/^pruning *=.*/pruning = \"$PRUNING\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \
\"$PRUNING_KEEP_RECENT\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \
\"$PRUNING_INTERVAL\"/" $HOME/.junction/config/app.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.junction/config/config.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.junction/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.00025amf\"/" $HOME/.junction/config/app.toml
```

**Create service**
```sudo tee /etc/systemd/system/junctiond.service > /dev/null << EOF
[Unit]
Description=Airchain Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=/usr/local/bin/junctiond start
[Install]
WantedBy=multi-user.target
EOF
```

**Services Management**

System reload
```
sudo systemctl daemon-reload
```

Start service
```
sudo service junctiond start
```

Start Node
```
junctiond start
```

Check logs
```
sudo journalctl -fu junctiond
```

Check Node status
```
junctiond status
```

**Create new wallet**
```
junctiond keys add <new-wallet>
```

**Create new validator**
```
junctiond comet show-validator
```

**Create validator file**
```
nano $HOME/.junction/config/validator.json
```

**Input data to validator.json file. Replace at "pubkey": {....} from show-validator output**
```
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"xxxxxxxxxxxxxxxxxxxxx="},
	"amount": "1000000amf",
	"moniker": "<validator-name>",
	"identity": "optional identity signature (ex. UPort or Keybase)",
	"website": "validator's (optional) website",
	"security": "validator's (optional) security contact email",
	"details": "validator's (optional) details",
	"commission-rate": "0.05",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}
```
Ctrl+o > Enter > Ctrl+x to save file & exit

**Check wallet balances**
```
junctiond q bank balances <yourwallet>
```

**Create VALIDATOR**
```
junctiond tx staking create-validator $HOME/.junction/config/validator.json --from wallet --chain-id junction --gas-prices 0.00025amf --gas-adjustment 1.5 --gas auto -y
```

**Check validator status**
```
junctiond status 2>&1 | jq .ValidatorInfo
```

```
junctiond status info
```

**Delegate to Yourself**
```
junctiond tx staking delegate $(junctiond keys show wallet --bech val -a) 0.1amf  --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```

**Genesis & Addrbook**
```
curl -Ls https://raw.githubusercontent.com/Apollo-Sync/Airchains/main/genesis.json
curl -Ls https://raw.githubusercontent.com/Apollo-Sync/Airchains/main/addrbook.json
```







