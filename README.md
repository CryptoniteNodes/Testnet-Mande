
<p align="center">
  <img src="https://user-images.githubusercontent.com/34649601/195998836-4f64c191-0a50-4819-a623-a2e9fb84901b.png" alt="Sublime's custom image"/>
</p>

# Mande Testnet-1  
I would like to introduce the new Mande testnet.  
There is no special difficulty in installation, just follow the instructions.
## Official Links
[Official Website](https://www.mande.network/)  
[Official Discord](https://discord.gg/nSQWuugJsx)  
[Official Documentation](https://github.com/mande-labs/testnet-1)
## Explorer
[STAVR](https://explorer.stavr.tech/mande-chain/)  
[Jambulmerah](https://explorer.jambulmerah.dev/mande-testnet/)  
[ANode](https://test.anode.team/mande-network/staking)

### **INCENTIVES TESTNET**  
**Tokenomics goes out in Nov 22**
  
## Date
Testnet 1 - from Oct 16 to Dec 22  
Testnet 2 - from Jan 23 to Feb 23
____  

## System Requirements
| CPU | RAM | Storage |
|----------------|:---------:|----------------:|
| >2 | 4GB | 100GB |  

### Update packages
    sudo apt update && sudo apt upgrade -y
### Install Depencies  
    sudo apt install curl tar wget tmux htop net-tools clang pkg-config libssl-dev jq build-essential git make ncdu -y
### Install Go 1.19
    ver="1.19" && \
    wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
    sudo rm -rf /usr/local/go && \
    sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
    rm "go$ver.linux-amd64.tar.gz" && \
    echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
    source $HOME/.bash_profile && \
    go version
![ALT](https://i2.paste.pics/17a9c44f092d8d725a384607fe5cfb80.png?trs=ed29bbf933f7d3206afcaa9d34f4211ff1141fa2219bfd931035df703c92ee7a)
### Download and build binaries
    cd $HOME
    curl -OL https://github.com/mande-labs/testnet-1/raw/main/mande-chaind
    mv mande-chaind /usr/local/bin
    chmod 744 /usr/local/bin/mande-chaind  
### Create wallet
    mande-chaind keys add <wallet>
`<Wallet>` - name your wallet  

**For recover wallet use:**  

    mande-chaind keys add <wallet> --recover
### Download genesis file
    wget -O $HOME/.mande-chain/config/genesis.json "https://raw.githubusercontent.com/mande-labs/testnet-1/main/genesis.json"
### Init
    mande-chaind init <node name> --chain-id mande-testnet-1
`<node name>` - create your node name
### Set up the minimum gas price,peers and seeds
    sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0mand\"/;" ~/.mande-chain/config/app.toml
    sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.mande-chain/config/config.toml
    peers="f9dec9a209fdcb22339d87eaadffde97d3ecf648@45.67.216.40:26656,c2ec4f71950d1d4e6233ed450b09f08d15ffbe98@195.201.165.123:10086,4fbbf09c5561c4a9692e368a672b99180b3f70ee@185.182.184.200:46656,156eb9c408b5274c14e7139fa14b3210de359848@5.161.113.160:26656,074a8eaf817da9df97c5becf367baaf2f3e1917f@135.125.163.63:26666,19a7467dc9aa99b3cdc8ee82492a57c4ffa46fc3@5.161.98.239:26656,2365cf1278df6bdf26b314d4f9c4e4108734b51d@144.126.156.253:26656,a3e3e20528604b26b792055be84e3fd4de70533b@38.242.199.93:24656"
    sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.mande-chain/config/config.toml
    seeds="cd3e4f5b7f5680bbd86a96b38bc122aa46668399@34.171.132.212:26656"
    sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.mande-chain/config/config.toml
### Config pruning
    pruning="custom" && \
    pruning_keep_recent="100" && \
    pruning_keep_every="0" && \
    pruning_interval="10" && \
    sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" ~/.mande-chain/config/app.toml && \
    sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" ~/.mande-chain/config/app.toml && \
    sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" ~/.mande-chain/config/app.toml && \
    sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" ~/.mande-chain/config/app.toml
### Indexer
    indexer="null" && \
    sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.mande-chain/config/config.toml
### Download addrbook
    wget -O $HOME/.mande-chain/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Mande%20Chain/addrbook.json"
### Create service file
    sudo tee /etc/systemd/system/mande-chaind.service > /dev/null <<EOF
    [Unit]
    Description=mande-chaind
    After=network-online.target

    [Service]
    User=$USER
    ExecStart=$(which mande-chaind) start
    Restart=on-failure
    RestartSec=3
    LimitNOFILE=65535

    [Install]
    WantedBy=multi-user.target
    EOF
### Start Node
    sudo systemctl daemon-reload
    sudo systemctl enable mande-chaind
    sudo systemctl restart mande-chaind && sudo journalctl -u mande-chaind -f -o cat
## StateSync(Optional)
    SNAP_RPC=http://38.242.199.93:24657
    peers="a3e3e20528604b26b792055be84e3fd4de70533b@38.242.199.93:24656"
    sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.mande-chain/config/config.toml
    LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
    BLOCK_HEIGHT=$((LATEST_HEIGHT - 500)); \
    TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

    echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

    sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
    s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
    s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
    s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
    s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.mande-chain/config/config.toml

    mande-chaind tendermint unsafe-reset-all --home /root/.mande-chain --keep-addr-book
    systemctl restart mande-chaind && journalctl -u mande-chaind -f -o cat
### Get Faucet
    curl -d '{"address":"<wallet adress>"}' -H 'Content-Type: application/json' http://35.224.207.121:8080/request
`<wallet adress>` - mande1ph799.....  
**If command faucet don`t work:**  
- Go to http://35.224.207.121/
- Connect a purse (To connect a wallet given to you on the server with the site you need to previously connect it to Keplr, using the secret phrase given when creating a purse)
- Click "Claim MAND"  
![](https://i2.paste.pics/bcdc5c363d5c64da7043f122b8416162.png)  
- **Check balance**
## Create validator
:exclamation:**AFTER YOUR NODE IS SYNCED**  - check `mande-chaind status 2>&1 | jq .SyncInfo` shoud be `false`

    mande-chaind tx staking create-validator \
    --chain-id mande-testnet-1 \
    --amount 0cred \
    --pubkey "$(mande-chaind tendermint show-validator)" \
    --from <wallet> \
    --moniker="<node name>" \
    --fees 1000mand
