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
PEERS="0b3f43387d737e0ec43069d9c60a38df749933e0@176.9.3.42:26656,c7617568e130086e8907a3bad2b925309ab113a2@152.53.108.33:63656,3250f8c73d5ded86fa5d0a7b78e84715b9c03643@88.198.46.55:19656,7db0e31a50244302ef1b6eb409834cc379e6a7f9@78.46.47.22:26656,a9ee14aac08cd57715086fd6759371cc434b4bcd@[2a01:4f8:171:325::2]:26656,1260d25e2cd9e3ab7f78d201f5b0c6711fdd2f4a@65.109.75.155:63656,84230c0e2f9a1e0dbd96dea52b9b90209be0478b@65.109.92.163:1020,d0cbeeeb6d16d82aa36e0f3936efc0f0918f8956@51.91.80.192:26656,62062b5ed6dd5b436fce06db833a85a22eec5f36@65.109.70.11:63656,1db4bf40135b6991b1594d44054aea5f74bb4d47@65.109.113.242:10656,75ce4b8573c385d63313b87cbbc938685c6453be@14.241.251.186:26656,a82f235048d52eb017ce847f1b910f9243da376a@158.220.114.0:26656,5880ddf4518b061c111ae6bf07b1ef76ef2a42af@158.220.100.154:26656,768ef12540a7b9e9a1cd44872510f9e63994f585@135.181.240.57:26656,c562dc9b08c602ca557a741168c52410e083231c@162.55.97.180:11756,2699379c4f0e3a17cf1cf6c6ed7f6a79a8fbb562@162.19.235.100:50512,ad4b0e43cce0ea5539e80cc96238a165e8c0cc8b@152.53.110.139:33656,5717aadf2f21e223012a1ab27e21307f510037c3@167.235.2.54:13756,2cac83c991358faf89f0c1bb40d94563609e00d9@65.109.84.33:26756,b012a4ceb39e60398d5b1f11ce497b62a136c4cb@144.76.70.103:11656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.junction/config/config.toml
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

**Launch Node**
```
sudo systemctl restart junctiond && journalctl -u junctiond -f -o cat
```







**Download Genesis**
```
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/genesis.json
cp genesis.json $HOME/.junction/config/genesis.json
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







