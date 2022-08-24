#!/bin/bash

while true
do

# Logo

echo "=================================================="
echo -e "\e[0m"
echo "   _  __        __        ___  __         __      ";
echo "  / |/ /__  ___/ /__ ___ / _ )/ /__  ____/ /__ ___";
echo " /    / _ \/ _  / -_|_-</ _  / / _ \/ __/  '_/(_-<";
echo "/_/|_/\___/\_,_/\__/___/____/_/\___/\__/_/\_\/___/";
echo "                                                  ";
echo -e "\e[0m"
echo "=================================================="  

echo -e '\e[36mWebsite:\e[39m' https://nodesblocks.dev
echo -e '\e[36mTwitter:\e[39m' https://twitter.com/nodesblocks
echo -e '\e[36mGithub:\e[39m'  https://github.com/NodesBlocks

# Menu

PS3='Select an action: '
options=(
"Install node"
"Check logs"
"Check balance"
"Request tokens"
"Create validator"
"Exit")
select opt in "${options[@]}"
do
case $opt in

"Install Node")
echo "============================================================"
echo "Your Node Name:"
echo "============================================================"
read NODENAME
echo "============================================================"
echo "Your Wallet Name:"
echo "============================================================"
read POINT_WALLET
echo export NODENAME=${NODENAME} >> $HOME/.bash_profile
echo export POINT_WALLET=${POINT_WALLET} >> $HOME/.bash_profile
echo export POINT_CHAIN_ID="point_10721-1" >> $HOME/.bash_profile
source ~/.bash_profile

#UPDATE APT
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y

#INSTALL GO
rm -r /usr/local/go
rm -r /usr/lib/go-1.13
wget https://golang.org/dl/go1.18.3.linux-amd64.tar.gz; \
rm -rv /usr/local/go; \
tar -C /usr/local -xzf go1.18.3.linux-amd64.tar.gz && \
rm -v go1.18.3.linux-amd64.tar.gz && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version

rm -rf $HOME/haqq $HOME/.evmosd
#INSTALL
cd $HOME && \
git clone https://github.com/pointnetwork/point-chain && \
cd point-chain && \
git checkout xnet-triton && \
make install

evmosd init $NODENAME --chain-id $POINT_CHAIN_ID


echo "============================================================"
echo "Save your mnemonic after entered passphrase !"
echo "============================================================"
#WALLET
evmosd keys add $POINT_WALLET

evmosd tendermint unsafe-reset-all --home $HOME/.evmosd
rm $HOME/.evmosd/config/genesis.json
wget -O $HOME/.evmosd/config/genesis.json "wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/testnet-xNet-Triton-1/genesis.json"
wget -O $HOME/.evmosd/config/config.toml "wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/testnet-xNet-Triton-1/config.toml"
wget -O $HOME/.evmosd/config/addrbook.json "https://raw.githubusercontent.com/StakeTake/guidecosmos/main/haqq/haqq_53211-1/addrbook.json"

SEEDS="8f7b0add0523ec3648cb48bc12ac35357b1a73ae@195.201.123.87:26656,899eb370da6930cf0bfe01478c82548bb7c71460@34.90.233.163:26656,f2a78c20d5bb567dd05d525b76324a45b5b7aa28@34.90.227.10:26656,4705cf12fb56d7f9eb7144937c9f1b1d8c7b6a4a@34.91.195.139:26656"
PEERS=""; \
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.evmosd/config/config.toml


# config pruning
indexer="null"
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"

sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.evmosd/config/config.toml
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.evmosd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.evmosd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.evmosd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.evmosd/config/app.toml



sudo tee /etc/systemd/system/evmosd.service > /dev/null <<EOF
[Unit]
Description=evmos
After=network-online.target
[Service]
User=$USER
ExecStart=$(which evmosd) start --home $HOME/.evmosd
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# start service
sudo systemctl daemon-reload
sudo systemctl enable evmosd
sudo systemctl restart evmosd

break
;;

"Check logs")

journalctl -u evmosd -f -o cat

break
;;


"Check balance")
evmosd q bank balances $(evmosd keys show $POINT_WALLET -a --bech acc)
break
;;

"Create validator")
evmosd tx staking create-validator \
  --amount=1000000000000000000000apoint \
  --pubkey=$(evmosd tendermint show-validator) \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000000000000000000" \
  --gas="400000" \
  --gas-prices="0.025apoint" \
  --from $POINT_WALLET \
  --moniker $NODENAME \
  --chain-id $POINT_CHAIN_ID \
  -y
break
;;

"Request tokens")
echo "========================================================================================================================"
echo "In order to receive tokens you need to go to the Discord server and request tokens in the validator channel"
echo "========================================================================================================================"

break
;;

"Exit")
exit
;;
*) echo "invalid option $REPLY";;
esac
done
done
