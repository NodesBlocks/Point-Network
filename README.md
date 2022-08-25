# Point Installation Guide

## Minimum Hardware Requirements

- 4 or more physical CPU cores
- At least 500GB of SSD disk storage
- At least 32GB of memory (RAM)
- At least 100mbps network bandwidth

# Point Full Node Installation Steps

## Automatic Installation with a Single Script

You can set up your Haqq fullnode in a few minutes using the automated script below. You will be asked for your node name (NODENAME) during the script!

```bash
wget -O point.sh https://raw.githubusercontent.com/NodesBlocks/Point-Network/main/point && chmod +x point.sh && ./point.sh
```
## Post-Installation Steps

You should make sure your validator syncs blocks. You can use the following command to check the sync status.
```bash
evmosd status 2>&1 | jq .SyncInfo
```
## Recover a Wallet

To recover your wallet using mnemonic:
```bash
evmosd keys add $POINT_WALLET --recover
```
To get the current wallet list
```bash
evmosd keys list
```
To check your wallet balance
```bash
evmosd query bank balances $POINT_WALLET
```
## Edit validator

If you can't see your balance in your wallet, chances are your node is still syncing. Please wait for the sync to finish and then continue

Edit a Validator:
```bash
evmosd tx staking edit-validator \
  --details "Details about your validator" \
  --website "Your website" \
  --identity "You can find it on keybase.io" \
  --security-contact "Your e-mail" \
  --moniker $NODENAME \
  --chain-id $PPOINT_CHAIN_ID \
```
# Useful Commands

***Service Management***

Check Logs:
```bash
journalctl -fu evmosd -o cat
```
Start Service:
```bash
systemctl start evmosd
```
Stop Service:
```bash
systemctl stop evmosd
```
Restart Service:
```bash
systemctl restart evmosd
```
***Node Information***

Sync Information:
```bash
evmosd status 2>&1 | jq .SyncInfo
```
Validator Information:
```bash
evmosd status 2>&1 | jq .ValidatorInfo
```
Node Information:
```bash
evmosd status 2>&1 | jq .NodeInfo
```
Show Node ID:
```bash
evmosd tendermint show-node-id
```
***Wallet Transactions***

List of Wallets:
```bash
evmosd keys list
```
Recover wallet using Mnemonic:
```bash
evmosd keys add $POINT_WALLET --recover
```
Wallet Delete:
```bash
evmosd keys delete $POINT_WALLET
```
Show Wallet Balance:
```bash
evmosd query bank balances $POINT_WALLET
```
Transfer tokens between wallets:
```bash
evmosd tx bank send $POINT_WALLET <TO_WALLET_ADDRESS> 10000000apoint
```
***Voting***
```bash
evmosd tx gov vote 1 yes --from $POINT_WALLET --chain-id=$POINT_CHAIN_ID
```
***Stake, Delegation and Rewards***

Delegate Process:
```bash
evmosd tx staking delegate YourValoperAddress 10000000apoint --from=$POINT_WALLET --chain-id=$POINT_CHAIN_ID --gas=auto --fees 250apoint
```
Redelegate from validator to another validator:
```bash
evmosd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000apoint --from=$POINT_WALLET --chain-id=$POINT_CHAIN_ID --gas=auto --fees 250apoint
```
Withdraw all rewards:
```bash
evmosd tx distribution withdraw-all-rewards --from=$POINT_WALLET --chain-id=$POINT_CHAIN_ID --gas=auto --fees 250apoint
```
Withdraw rewards with commission:
```bash
evmosd tx distribution withdraw-rewards YourValoperAddress --from=$POINT_WALLET --commission --chain-id=$PPOINT_CHAIN_ID
```
Get Out Of Jail(Unjail):
```bash
evmosd tx slashing unjail \
  --broadcast-mode=block \
  --from=$POINT_WALLET \
  --chain-id=$PPOINT_CHAIN_ID \
```
To Delete Node Completely:
```bash
sudo systemctl stop evmosd
sudo systemctl disable evmosd
sudo rm /etc/systemd/system/evmosd* -rf
sudo rm $(which evmosd) -rf
sudo rm $HOME/.evmosd* -rf
sudo rm $HOME/evmosd -rf
sed -i '/point_/d' ~/.bash_profile
```
