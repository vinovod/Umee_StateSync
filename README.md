### This document briefly describes how to start the node from StateSync Provider

1. Check wheither provider address is availabe: 213.246.39.63:26657
2. Set variables with data for StateSync
```bash
RPC="213.246.39.63:26657"
RECENT_HEIGHT=$(curl -s $RPC/block | jq -r .result.block.header.height)
TRUST_HEIGHT=$((RECENT_HEIGHT - 500))
TRUST_HASH=$(curl -s "$RPC/block?height=$TRUST_HEIGHT" | jq -r .result.block_id.hash)
PEER="d40580805488ee7b0489241360dfd0b0fa7b328e@213.246.39.53:26656,f96136ef2ca1f0a4d720ade2c6d07497faaef721@213.246.39.63:26656"
```
3. Check wheither variables are set correctly
```bash
echo $RPC, $RECENT_HEIGHT, $TRUST_HEIGHT, $TRUST_HASH, $PEER
```
4. Make config.toml changes
```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$TRUST_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.umee/config/config.toml

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEER\"/" $HOME/.umee/config/config.toml
```
5. Stop the node and reset local blockchain. Node config will not be affected.
```bash
systemctl stop umeed && umeed unsafe-reset-all
```
6. Restart the node and see logs for StateSync proccess
```bash
systemctl restart umeed && journalctl -u umeed -f -o cat
```
