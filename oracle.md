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
git clone https://github.com/Team-Kujira/oracle-price-feeder
cd oracle-price-feeder
make install
price-feeder version
```
```
kujirad keys add oracle
```
```
mkdir ~/.kujira/keyring-file
mv ~/.kujira/*.info ~/.kujira/*.address ~/.kujira/keyring-file
```
```
kujirad keys add wallet --recover
```
```
kujirad keys add oracle --recover
```
```
kujirad tx oracle set-feeder <oracle_wallet> --from <validator_wallet> --fees 250ukuji
```
```
cd
nano config.toml
```
```
gas_adjustment = 1.7
gas_prices = "0.00125ukuji"
enable_server = true
enable_voter = true
provider_timeout = "500ms"

[server]
listen_addr = "0.0.0.0:7171"
read_timeout = "20s"
verbose_cors = true
write_timeout = "20s"

[[deviation_thresholds]]
base = "USDT"
threshold = "2"

[[currency_pairs]]
base = "ATOM"
providers = [
  "binance",
  "kraken",
  "osmosis",
]
quote = "USD"

[[currency_pairs]]
base = "DOT"
providers = ["binance", "kraken", "coinbase"]
quote = "USD"

[[currency_pairs]]
base = "ETH"
providers = ["binance", "kraken", "coinbase"]
quote = "USD"

[[currency_pairs]]
base = "BTC"
providers = ["binance", "kraken", "coinbase"]
quote = "USD"

[account]
fee_granter = "kujira1vkje22mayn72r0a7kna6agv0sqm6k94ry9k6dd"
address = "ORACLE"
chain_id = "kaiyo-1"
validator = "WALLET"
prefix = "kujira"

[keyring]
backend = "file"
dir = "/root/.kujira"

[rpc]
grpc_endpoint = "localhost:9090"
rpc_timeout = "100ms"
tmrpc_endpoint = "http://localhost:26657"

[telemetry]
enable_hostname = true
enable_hostname_label = true
enable_service_label = true
enabled = true
global_labels = [["chain-id", "kaiyo-1"]]
service_name = "price-feeder"
type = "prometheus"

[[provider_endpoints]]
name = "binance"
rest = "https://api1.binance.us"
websocket = "stream.binance.us:9443"
```
```
echo <keyring_password> | price-feeder ~/config.toml
```
```
nano /etc/systemd/system/kujira-price-feeder.service
```
```
[Unit]
Description=kujira-price-feeder
After=network.target

[Service]
Type=simple
User=root
ExecStart=/root/go/bin/price-feeder /root/config.toml
Restart=on-abort
LimitNOFILE=65535
Environment="PRICE_FEEDER_PASS=PASS"

[Install]
WantedBy=multi-user.target
```
```
sudo systemctl daemon-reload
```
```
sudo systemctl start kujira-price-feeder
```
```
sudo systemctl enable kujira-price-feeder
```
```
sudo journalctl -fu kujira-price-feeder
```

