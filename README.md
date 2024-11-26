Manual Installation
Official Documentation
Recommended Hardware: 6 Cores, 16GB RAM, 400GB of storage (NVME), 100 Mb/s

**install dependencies, if needed**
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

**install go, if needed**
```
cd $HOME
VER="1.22.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

**set vars**
```
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export STORY_CHAIN_ID="odyssey"" >> $HOME/.bash_profile
echo "export STORY_PORT="52"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

**download binaries**
```
cd $HOME
wget -O geth https://github.com/piplabs/story-geth/releases/download/v0.10.0/geth-linux-amd64
chmod +x $HOME/geth
mv $HOME/geth ~/go/bin/
[ ! -d "$HOME/.story/story" ] && mkdir -p "$HOME/.story/story"
[ ! -d "$HOME/.story/geth" ] && mkdir -p "$HOME/.story/geth"
```

**install Story**
```
cd $HOME
rm -rf story
git clone https://github.com/piplabs/story
cd story
git checkout v0.12.0
go build -o story ./client 
mv $HOME/story/story $HOME/go/bin/
```

**init story app**
```
story init --moniker test --network odyssey
```

**set seeds and peers**
```
SEEDS="434af9dae402ab9f1c8a8fc15eae2d68b5be3387@story-testnet-seed.itrocket.net:29656"
PEERS="c2a6cc9b3fa468624b2683b54790eb339db45cbf@story-testnet-peer.itrocket.net:26656,75ac7b193e93e928d6c83c273397517cb60603c0@3.142.16.95:26656,63be7e3effbba063b32af7aa630d7ba270b05060@65.21.225.188:10056,89a07021f98914fbac07aae9fbb12a92c5b6b781@152.53.102.226:26656,20b8296d4019cdc0b72773ea7984523af40feacb@148.72.138.4:26656,bd58bf29180f476bd250af22d6026559d7eff289@146.59.118.198:26656,c2d05fe74c762fed39487d146e72f00c725db21c@157.173.119.101:656,4f7931c2c795da73d7ea20f0166791f134f6e45e@2.59.117.67:26656,2086affe2a3ea6ba3a9e6ca16a3ba406906f6eea@141.98.217.151:26656,5c713a045806be88171d27b837ea4dbcd5dbf5c0@68.183.231.160:26656,e22f70a71859d0156778ac328b3f331689094f56@15.235.225.47:26656,07ab4164e1d0ee17c565542856ac58981537156f@185.16.38.165:42656,04e5734295da362f09a61dd0a9999449448a0e5c@52.14.39.177:26656,4d881b88285d41d2f6c33fdf50a307666aacaca1@65.109.36.231:52656,dfcc85577f77f3c312fd6e803be84b09919ead59@157.173.204.15:26656,65c49a2c4c73ac6f12675f0051f4332b1320d384@213.239.198.181:35656,7ecb96bb4778b3f979be7c8e720cc9ee739ac770@104.198.43.15:26656,0f1b1840d2f3302b60a671be500b181eb354e21f@5.9.117.149:45656,c5c214377b438742523749bb43a2176ec9ec983c@176.9.54.69:26656,5dec0b793789d85c28b1619bffab30d5668039b7@150.136.113.152:26656,443896c7ec4c695234467da5e503c78fcd75c18e@80.241.215.215:26656,201ce716a03aa45a7e037575a996a5082348eb44@148.72.138.7:26656,b5aab33974fe219a5525a4454d180216c4dd3304@[2001:41d0:203:e4db::6]:26656,396710d357b98220bc5f9c4d11c56392db631c30@161.97.174.80:21656,cd33da43885ce09266d5019d996f2094a5521b85@81.0.246.245:656,25e55baf483439655d9d2258b7006d35bcee0a4d@157.173.121.2:656,02e9fac0fab468724db00e5e3328b2cbca258fdc@95.217.193.182:26656,959ef7ebaaacd08de053e738707d3a2940f846a4@148.72.138.5:26656,fa294c4091379f84d0fc4a27e6163c956fc08e73@65.108.103.184:26656,3508ef280392bd431ea078dec16dcfae89e8eb78@213.239.192.18:26656,f5746b45e50f5b8ee4dd87c216e66c6ea4ba1e05@95.217.74.22:26656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.story/story/config/config.toml
```

**download genesis and addrbook**
```
wget -O $HOME/.story/story/config/genesis.json https://server-3.itrocket.net/testnet/story/genesis.json
wget -O $HOME/.story/story/config/addrbook.json  https://server-3.itrocket.net/testnet/story/addrbook.json
```

**set custom ports in story.toml file**
```
sed -i.bak -e "s%:1317%:${STORY_PORT}317%g;
s%:8551%:${STORY_PORT}551%g" $HOME/.story/story/config/story.toml
```

**set custom ports in config.toml file**
```
sed -i.bak -e "s%:26658%:${STORY_PORT}658%g;
s%:26657%:${STORY_PORT}657%g;
s%:26656%:${STORY_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${STORY_PORT}656\"%;
s%:26660%:${STORY_PORT}660%g" $HOME/.story/story/config/config.toml
```

**enable prometheus and disable indexing**
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.story/story/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.story/story/config/config.toml
```

**create geth servie file**
```
sudo tee /etc/systemd/system/story-geth.service > /dev/null <<EOF
[Unit]
Description=Story Geth daemon
After=network-online.target
```

[Service]
User=$USER
ExecStart=$HOME/go/bin/geth --odyssey --syncmode full --http --http.api eth,net,web3,engine --http.vhosts '*' --http.addr 0.0.0.0 --http.port ${STORY_PORT}545 --authrpc.port ${STORY_PORT}551 --ws --ws.api eth,web3,net,txpool --ws.addr 0.0.0.0 --ws.port ${STORY_PORT}546
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

**create story service file**
```
sudo tee /etc/systemd/system/story.service > /dev/null <<EOF
[Unit]
Description=Story Service
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/.story/story
ExecStart=$(which story) run

Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

**download snapshots**
backup priv_validator_state.json
```
cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/story/priv_validator_state.json.backup
```

**remove old data and unpack Story snapshot**
```
rm -rf $HOME/.story/story/data
curl https://server-3.itrocket.net/testnet/story/story_2024-11-02_226713_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/story
```

**restore priv_validator_state.json**
```
mv $HOME/.story/story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json
```

**delete geth data and unpack Geth snapshot**
```
rm -rf $HOME/.story/geth/odyssey/geth/chaindata
mkdir -p $HOME/.story/geth/odyssey/geth
curl https://server-3.itrocket.net/testnet/story/geth_story_2024-11-02_226713_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/geth/odyssey/geth
```

**enable and start geth, story**
```
sudo systemctl daemon-reload
sudo systemctl enable story story-geth
sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story
```

**check logs**
```
journalctl -u story -u story-geth -f
Automatic Installation
source <(curl -s https://itrocket.net/api/testnet/story/story-autoinstall/)
```

**reate validator**

**View your validator key**
```
story validator export
```

**Export EVM private key**
```
story validator export --export-evm-key
```

**View EVM private key and make a key backup**
```
cat $HOME/.story/story/config/private_key.txt
```

Use this private key to import your account into a wallet, e.g. Metamask or Phantom. Add the odyssey testnet to your wallet via faucet. Then, copy your 'EVM address' from the wallet and request $IP tokens. Now you can see the balance and make transactions in the wallet app.

**Before creating a validator, wait for your node to get fully synced. Once "catching_up" is "false", move on to the next step**
```
curl localhost:$(sed -n '/\[rpc\]/,/laddr/ { /laddr/ {s/.*://; s/".*//; p} }' $HOME/.story/story/config/config.toml)/status | jq
```

Create validator
story validator create --stake 1000000000000000000 --private-key $(cat $HOME/.story/story/config/private_key.txt | grep "PRIVATE_KEY" | awk -F'=' '{print $2}')
Remember to backup your validator priv_key from here:

cat $HOME/.story/story/config/priv_validator_key.json
Firewall rules
Configure firewall rules:

sudo ufw allow 30303/tcp comment geth_p2p_port
sudo ufw allow 26656/tcp comment story_p2p_port
Delete node
sudo systemctl stop story story-geth
sudo systemctl disable story story-geth
rm -rf $HOME/.story
sudo rm /etc/systemd/system/story.service /etc/systemd/system/story-geth.service
sudo systemctl daemon-reload
