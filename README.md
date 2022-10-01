### This document briefly describes how to start the node from StateSync Provider

1. Check wheither provider address is availabe: 178.170.49.138:26657
2. Set variables with data for StateSync
```bash
RPC="178.170.49.138:26657"
RECENT_HEIGHT=$(curl -s $RPC/block | jq -r .result.block.header.height)
TRUST_HEIGHT=$((RECENT_HEIGHT - 500))
TRUST_HASH=$(curl -s "$RPC/block?height=$TRUST_HEIGHT" | jq -r .result.block_id.hash)
PEER="1eb144f6389b0d174b4227f9642ffcd8cee00cc2@178.170.49.138:26656,b5a58e36171fb3bb0dc60c80010d889b7be17ce1@65.108.141.96:26656"
```
3. Check wheither variables are set correctly
```bash
echo $RPC, $RECENT_HEIGHT, $TRUST_HEIGHT, $TRUST_HASH, $PEER
```
4. Make config.toml and app.toml changes
```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$TRUST_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.umee/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEER\"/" $HOME/.umee/config/config.toml
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = 500/" $HOME/.umee/config/app.toml
```
5. Stop the node and reset local blockchain. Node config will not be affected.
```bash
systemctl stop umeed && umeed unsafe-reset-all
```
6. Restart the node and see logs for StateSync proccess
```bash
systemctl restart umeed && journalctl -u umeed -f -o cat
```
