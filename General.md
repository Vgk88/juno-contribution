# Guide_Cyb_Juno


# Mainnet
* [Public API]()
* [Snapshot]()
* [State Sync]()

# Testnet
* [State Sync]()



### Public API
<details open>
  <summary><b>Сontents</b></summary>

RPC Endpoin
```
http://65.21.132.27:28057
```

LCD (Rest) API Endpoint
```
http://65.21.132.27:1317
```

GRPC Endpoint
```
http://65.21.132.27:9800
```
</details>

## Snapshot Mainnet

### Stop the service and remove the data

```
sudo systemctl stop junod
cp $HOME/.juno/data/priv_validator_state.json $HOME/.juno/priv_validator_state.json.backup
rm -rf $HOME/.juno/data
```


### Download latest snapshot
```
cd $HOME/.juno/
wget http://65.21.132.27/Snapshot_Juno_Main.tar.gz -O - | tar -xz
cp $HOME/.juno/priv_validator_state.json.backup $HOME/.juno/data/priv_validator_state.json
rm $HOME/.juno/Snapshot_Jackal-test.tar.gz
```

### Restart the service and check the log
```
sudo systemctl restart junod && sudo journalctl -u junod -f --no-hostname -o cat
```

  
## State Sync Mainnet

<details>
  <summary><b>Pruning</b></summary>

```
# Prune Type
pruning = "custom"

# Prune Strategy
pruning-keep-every = 2000

# State-Sync Snapshot Strategy
snapshot-interval = 2000
snapshot-keep-recent = 5
```
</details>

```
SNAP_RPC="http://65.21.132.27:28057"
Name_bin="junod"
Name_config_file=".juno"
Name_service="junod"
peers="dc33460b6b180ced6d4facce0281277f462e68dc@65.21.132.27:28056"
```

```
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/$Name_config_file/config/config.toml

```

```
sudo systemctl stop $Name_service && $Name_bin tendermint unsafe-reset-all --home $HOME/$Name_config_file --keep-addr-book

```

```
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/$Name_config_file/config/config.toml

```

```
sudo systemctl restart $Name_service
journalctl -fu $Name_service -o cat

```


## State Sync Testnet

<details>
  <summary><b>Pruning</b></summary>

```
# Prune Type
pruning = "default"

# Prune Strategy
pruning-keep-every = 0

# State-Sync Snapshot Strategy
snapshot-interval = 0
snapshot-keep-recent = 0
```
</details>

```
SNAP_RPC="http://135.181.116.109:28057"
Name_bin="junod"
Name_config_file=".juno"
Name_service="junod"
peers="247504c3c1b7f78f6a7a9aee0714734ceb317079@135.181.116.109:28056"
```

```
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/$Name_config_file/config/config.toml

```

```
sudo systemctl stop $Name_service && $Name_bin tendermint unsafe-reset-all --home $HOME/$Name_config_file --keep-addr-book

```

```
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/$Name_config_file/config/config.toml

```

```
sudo systemctl restart $Name_service
journalctl -fu $Name_service -o cat

```
