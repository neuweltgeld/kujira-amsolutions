```
apt update && apt upgrade -y
```

```
apt install build-essential git curl gcc make jq -y
```

```
wget -c https://go.dev/dl/go1.18.3.linux-amd64.tar.gz && rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.3.linux-amd64.tar.gz && rm -rf go1.18.3.linux-amd64.tar.gz
```

```
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
```

```
git clone https://github.com/Team-Kujira/core $HOME/kujira-core \
&& cd kujira-core; git checkout tags/v0.6.4 && make install \
&& kujirad version
```

```
kujirad init <moniker> --chain-id=kaiyo-1
```

```
wget -q -O $HOME/.kujira/config/genesis.json https://raw.githubusercontent.com/Team-Kujira/networks/master/mainnet/kaiyo-1.json
```

```
$HOME/.kujira/config/app.toml
```

```
minimum-gas-prices = "0.00119ukuji,0.00150ibc/295548A78785A1007F232DE286149A6FF512F180AF5657780FC89C009E2C348F,0.000125ibc/27394FB092D2ECCD56123C74F36E4C1F926001CEADA9CA97EA622B25F41E5EB2,0.00126ibc/47BD209179859CDE4A2806763D7189B6E6FE13A17880FE2B42DE1E6C1E329E23,0.00652ibc/3607EB5B5E64DD1C0E12E07F077FF470D5BC4706AFCBC98FE1BA960E5AE4CE07,617283951ibc/F3AA7EF362EC5E791FE78A0F4CCC69FEE1F9A7485EB1A8CAB3F6601C00522F10,0.000288ibc/EFF323CC632EC4F747C61BCE238A758EFDB7699C3226565F7C20DA06509D59A5,5ibc/DA59C009A0B3B95E0549E6BF7B075C8239285989FF457A8EDDBB56F10B2A6986,0.00137ibc/A358D7F19237777AF6D8AD0E0F53268F8B18AE8A53ED318095C14D6D7F3B2DB5,0.0488ibc/4F393C3FCA4190C0A6756CE7F6D897D5D1BE57D6CCB80D0BC87393566A7B6602,78492936ibc/004EBF085BBED1029326D56BE8A2E67C08CECE670A94AC1947DF413EF5130EB2,964351ibc/1B38805B1C75352B28169284F96DF56BDEBD9E8FAC005BDCC8CF0378C82AA8E7"
```

```
sed -i "s/^timeout_commit =.*/timeout_commit = \"1500ms\"/" $HOME/.kujira/config/config.toml
```


```
echo "[Unit]
Description=Kujirad Node
After=network.target
#
[Service]
User=$USER
Type=simple
ExecStart=$(which kujirad) start
RestartSec=10
Restart=on-failure
LimitNOFILE=65535
#
[Install]
WantedBy=multi-user.target" > kujirad.service \
&& sudo cp kujirad.service /etc/systemd/system

kujirad tendermint unsafe-reset-all --home $HOME/.kujira
 
SNAP_RPC="https://kujira-rpc.polkachu.com:443"; \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) \
&& echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.kujira/config/config.toml

peers="1048e73299d435b6598245d246562efc62df002d@65.108.128.201:11856"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.kujira/config/config.toml;

sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

# add addrbook manually.


```
sudo systemctl enable kujirad.service && sudo systemctl daemon-reload \
&& sudo systemctl restart kujirad && sudo journalctl -u kujirad -f -o cat
```

```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.kujira/config/config.toml
```

```
kujirad status 2>&1 | jq .SyncInfo
```
```
sudo journalctl -u kujirad -f -o cat
```
