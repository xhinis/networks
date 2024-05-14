<h1 align="center"> Initia

![image](https://i.imgur.com/qb495Kb.png)


</h1>

 * [Discord](https://discord.gg/initia)<br>
 * [Twitter](https://twitter.com/initiaFDN)<br>
 * [Initia Website](https://initia.xyz/)<br>
 * [Initia Blog](https://medium.com/@initiafdn/the-validator-initiation-a-guide-to-initias-incentivized-testnet-for-validators-b6ddd2eff632)<br>
 


### System Requirements

| Component | Minimum Requirements |
|---|---|
| CPU | 4 |
| RAM | 16+ GB |
| Storage | 1 TB SSD |
| Network Bandwidth | 100 Mbps |

### Required Installations

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### Go Installation

```
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

###  Downloading Files

```
wget https://initia.s3.ap-southeast-1.amazonaws.com/initiation-1/initia_v0.2.10_Linux_x86_64.tar.gz
tar -zxvf initia_v0.2.10_Linux_x86_64.tar.gz
sudo mv initia_v0.2.10_Linux_x86_64/initiad /usr/local/bin/
```


###  Moniker
Change [moniker] with your moniker. Example: Rayz

```
initiad init [moniker] --chain-id initiation-1
curl -Ls https://github.com/0glabs/0g-evmos/releases/download/v1.0.0-testnet/genesis.json > $HOME/.initia/config/genesis.json
```

###  Create Service

```
sudo tee /etc/systemd/system/initiad.service > /dev/null << EOF
[Unit]
Description=initia node
After=network-online.target

[Service]
User=$USER
ExecStart=/usr/bin/initiad start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000

[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable initiad.service
```

###  Genesis Addrbook

```
wget https://initia.s3.ap-southeast-1.amazonaws.com/initiation-1/genesis.json -O $HOME/.initia/config/genesis.json
```

###  Seed

```
PEERS="40d3f977d97d3c02bd5835070cc139f289e774da@168.119.10.134:26313,841c6a4b2a3d5d59bb116cc549565c8a16b7fae1@23.88.49.233:26656,e6a35b95ec73e511ef352085cb300e257536e075@37.252.186.213:26656,2a574706e4a1eba0e5e46733c232849778faf93b@84.247.137.184:53456,ff9dbc6bb53227ef94dc75ab1ddcaeb2404e1b0b@178.170.47.171:26656,edcc2c7098c42ee348e50ac2242ff897f51405e9@65.109.34.205:36656,07632ab562028c3394ee8e78823069bfc8de7b4c@37.27.52.25:19656,028999a1696b45863ff84df12ebf2aebc5d40c2d@37.27.48.77:26656,140c332230ac19f118e5882deaf00906a1dba467@185.219.142.119:53456,1f6633bc18eb06b6c0cab97d72c585a6d7a207bc@65.109.59.22:25756,065f64fab28cb0d06a7841887d5b469ec58a0116@84.247.137.200:53456,767fdcfdb0998209834b929c59a2b57d474cc496@207.148.114.112:26656,093e1b89a498b6a8760ad2188fbda30a05e4f300@35.240.207.217:26656,12526b1e95e7ef07a3eb874465662885a586e095@95.216.78.111:26656" && \
SEEDS="2eaa272622d1ba6796100ab39f58c75d458b9dbc@34.142.181.82:26656,c28827cb96c14c905b127b92065a3fb4cd77d7f6@testnet-seeds.whispernode.com:25756" && \
sed -i \
    -e "s/^seeds *=.*/seeds = \"$SEEDS\"/" \
    -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" \
    "$HOME/.initia/config/config.toml"
```


### Seed

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.15uinit,0.01uusdc\"/" $HOME/.initia/config/app.toml
```

### Pruning

```
sed -i \
    -e "s/^pruning *=.*/pruning = \"custom\"/" \
    -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" \
    -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" \
    "$HOME/.initia/config/app.toml"
```

### Snap:

```
sudo apt install liblz4-tool

sudo systemctl stop initiad

initiad tendermint unsafe-reset-all --home $HOME/.initia --keep-addr-book

wget https://rpc-initia-testnet.trusted-point.com/latest_snapshot.tar.lz4

lz4 -d -c ./latest_snapshot.tar.lz4 | tar -xf - -C $HOME/.initia

sudo systemctl restart initiad && sudo journalctl -u initiad -f -o cat

```

### Start:

```
sudo systemctl restart initiad

```

### Log:

```
sudo journalctl -u initiad -f -o cat
```

### Create Wallet:

- Don't forget to save mnemonics of your wallet.
- Write your own wallet-name

```bash
initiad keys add wallet-name
```


### FAUCET:
Request for a faucet to your wallet that you created before creating validator.

 [https://faucet.testnet.initia.xyz/](https://faucet.testnet.initia.xyz/)

### Create Validator:

Change MONIKAR_NAME with yours. Example: Rayz

initiad tx mstaking create-validator \
    --amount=5000000uinit \   # It can be other LP tokens 
    --pubkey=$(initiad tendermint show-validator) \
    --moniker="<your_moniker>" \
    --chain-id=<chain_id> \
    --from=<key_name> \
    --commission-rate="0.10" \
    --commission-max-rate="0.20" \
    --commission-max-change-rate="0.01" \
    --identity=<keybase_identity> # (optional) for validator image
