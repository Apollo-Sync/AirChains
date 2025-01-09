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
sudo apt update && \
sudo apt install curl git jq build-essential gcc unzip wget lz4 -y
```

**Install Go**
```
cd $HOME && \
ver="1.22.2" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
**Download the source files:**
```
wget https://github.com/ignite/cli/releases/download/v0.27.1/ignite_0.27.1_linux_amd64.tar.gz
```

**Modify file permissions:**
```
chmod +x ignite_0.27.1_linux_amd64.tar.gz
```

**Extract the TAR file:**
```
tar -xvf ignite_0.27.1_linux_amd64.tar.gz
```

**Move the binary:**
```
sudo mv ignite /usr/local/bin
```

**Verify installation:**
```
ignite version
```

**Set up environment variables:**
```
export PATH=$PATH:/usr/local/go/bin
```


**Running a Full Node**



**Download binary**
```
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
```

**Make the binary executable**
```
chmod +x junctiond
```

**Move the binary to a system-wide directory**
```
sudo mv junctiond /usr/local/bin
```

**Initialize the Node with the Moniker**
```
junctiond init <moniker>
```

**Update Genesis Configuration**
```
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/genesis.json
```

**Replace the Existing Genesis File**
```
cp genesis.json ~/.junction/config/genesis.json
```

**add peers**
```
PEERS="e929f77cfe4cd7d51433f438d3b764937e799313@airchains-testnet-peer.itrocket.net:19656,f0e05f00d4dd483edf300bd93fbace9f3e0d5a0a@91.227.33.18:19656,5880ddf4518b061c111ae6bf07b1ef76ef2a42af@158.220.100.154:26656,8997abdef4363d7225390d4f6fd1cc1dde15f4d9@65.21.221.110:63656,e00222e8db843c99acafe0a6dc0aebd3a95e813f@65.108.233.73:19656,859485b13c2d8ab3888ffc11d1c506d78f681317@5.9.116.21:26756,d5ded9ed366f251a59c85f84ed1fa825cceb0d97@[2a01:4f8:221:158e::2]:13656,0d03e79ef79687421ac6f4b1ddd6add67dd2d6a0@65.109.83.40:28156,0305205b9c2c76557381ed71ac23244558a51099@162.55.65.162:26656,2cac83c991358faf89f0c1bb40d94563609e00d9@65.109.84.33:26756,84230c0e2f9a1e0dbd96dea52b9b90209be0478b@65.109.92.163:1020"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.junction/config/config.toml
```

**Set Mini Gas**
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.00025amf"|g' $HOME/.junction//config/app.toml
```

**create service**
```
sudo tee /etc/systemd/system/junctiond.service > /dev/null << EOF
[Unit]
Description=airchains node service
After=network-online.target
[Service]
User=$USER
ExecStart=/usr/local/bin/junctiond start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable junctiond
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







