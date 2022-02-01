AGORIC mainnet0 SETUP Commands:

#Stop previous Agoric Service if there is
systemctl stop ag-chain-cosmos

git clone https://github.com/Agoric/ag0
cd ag0
git checkout agoric-3.1
make build
build/ag0
build/ag0 version --long

# First, get the network config for the current network.
curl https://main.agoric.net/network-config > chain.json
# Set chain name to the correct value
chainName=`jq -r .chainName < chain.json`
# Confirm value: should be something like agorictest-N.
echo $chainName

# Replace <your_moniker> with the public name of your node.
# NOTE: The `--home` flag (or `AG_CHAIN_COSMOS_HOME` environment variable) determines where the chain state is stored.
# By default, this is `$HOME/.agoric`.
build/ag0 init --chain-id $chainName StakeITeasy

# Download the genesis file
curl https://main.agoric.net/genesis.json > $HOME/.agoric/config/genesis.json 
# Reset the state of your validator.
build/ag0 unsafe-reset-all

# Set peers variable to the correct value
peers=$(jq '.peers | join(",")' < chain.json)
# Set seeds variable to the correct value.
seeds=$(jq '.seeds | join(",")' < chain.json)
# Confirm values, each should be something like "077c58e4b207d02bbbb1b68d6e7e1df08ce18a8a@178.62.245.23:26656,..."
echo $peers
echo $seeds
# Fix `Error: failed to parse log level`
sed -i.bak 's/^log_level/# log_level/' $HOME/.agoric/config/config.toml
# Replace the seeds and persistent_peers values
sed -i.bak -e "s/^seeds *=.*/seeds = $seeds/; s/^persistent_peers *=.*/persistent_peers = $peers/" $HOME/.agoric/config/config.toml

sudo tee <<EOF >/dev/null /etc/systemd/system/agd.service
[Unit]
Description=Agoric Cosmos daemon
After=network-online.target

[Service]
User=$USER
ExecStart=/root/ag0/build/ag0 start --log_level=warn
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF

# Check the contents of the file, especially User, Environment and ExecStart lines
cat /etc/systemd/system/agd.service

# Start the node
sudo systemctl enable agd
sudo systemctl daemon-reload
sudo systemctl start agd

journalctl -u agd -f

build/ag0 status | jq .SyncInfo

build/ag0 query bank balances 
build/ag0 tx bank send <from> <to> 100000000ubld
build/ag0 keys add --recover  <ADDRESS_NAME>

build/ag0 tendermint show-validator
{"@type":"/cosmos.crypto....

# Set the chainName value again
chainName=`curl https://main.agoric.net/network-config | jq -r .chainName`
echo $chainName
# Replace <key_name> with the key you created previously
build/ag0 tx staking create-validator \
  --amount=<YOUR STAKE>ubld \
  --broadcast-mode=block \
  --pubkey='{"@type":"/cosmos.crypto....' \
  --moniker=BlockShark \
  --website="https://www.blockshark.net" \
  --details="Your reliable partner for staking on Proof-of-Stake blockchains" \
  --commission-rate="0.09" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --from=Rewards \
  --chain-id=$chainName \
  --gas=auto \
  --gas-adjustment=1.4

build/ag0 tx staking edit-validator --gas auto -y --chain-id agoric-3 --from vah-rewards --moniker StakeITeasy --identity=D417A6F1732566F6

build/ag0 status 2>&1 | jq .ValidatorInfo

build/ag0 tx staking delegate agoricvaloper15g36t36cf8gpfcw6prdk6nyt7s66cex5fkqz69 100.000000ubld --from=<Account_Name> --chain-id=agoric-3 --gas=auto



ag0 tx staking create-validator \
  --amount=48093000000ubld \
  --broadcast-mode=block \
  --pubkey='{"@type":"/cosmos.crypto.ed25519.PubKey","key":"fAlP+ttcjRlaXyFbpazGnISI4SdC3FiG7lGeQoVGECE="}' \
  --moniker=StakeITeasy \
  --website="https://stakeiteasy.ru" \
  --details="High speed, safe, fault tolerant validator with professional team doing best to get maximum profit for you. Relax. Stakeiteasy!" \
  --commission-rate="0.09" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --from=vah-rewards \
  --chain-id=$chainName \
  --gas=auto \
  --gas-adjustment=1.4



root@agoric-main-0:~/ag0# build/ag0 tx staking create-validator   --amount=1000000ubld   --broadcast-mode=block   --pubkey='{"@type":"/cosmos.crypto.ed25519.PubKey","key":"fAlP+ttcjRlaXyFbpazGnISI4SdC3FiG7lGeQoVGECE="}'   --moniker= StakeITeasy   --website="https://stakeiteasy.ru"   --details="High speed, safe, fault tolerant validator with professional team doing best to get maximum profit for you. Relax. Stakeiteasy!"   --commission-rate="0.09"   --commission-max-rate="0.20"   --commission-max-change-rate="0.01"   --min-self-delegation="1"   --from=vah-rewards  --chain-id=$chainName   --gas=auto   --gas-adjustment=1.4
Enter keyring passphrase:
gas estimate: 265984
{"body":{"messages":[{"@type":"/cosmos.staking.v1beta1.MsgCreateValidator","description":{"moniker":"","identity":"","website":"https://stakeiteasy.ru","security_contact":"","details":"High speed, safe, fault tolerant validator with professional team doing best to get maximum profit for you. Relax. Stakeiteasy!"},"commission":{"rate":"0.090000000000000000","max_rate":"0.200000000000000000","max_change_rate":"0.010000000000000000"},"min_self_delegation":"1","delegator_address":"agoric1pmwdmxg75lt7ahd630r7lf7k5rx2ff8jmh8s9j","validator_address":"agoricvaloper1pmwdmxg75lt7ahd630r7lf7k5rx2ff8jt05een","pubkey":{"@type":"/cosmos.crypto.ed25519.PubKey","key":"fAlP+ttcjRlaXyFbpazGnISI4SdC3FiG7lGeQoVGECE="},"value":{"denom":"ubld","amount":"1000000"}}],"memo":"","timeout_height":"0","extension_options":[],"non_critical_extension_options":[]},"auth_info":{"signer_infos":[],"fee":{"amount":[],"gas_limit":"265984","payer":"","granter":""}},"signatures":[]}

confirm transaction before signing and broadcasting [y/N]: y
code: 0
codespace: ""
data: 0A2C0A2A2F636F736D6F732E7374616B696E672E763162657461312E4D736743726561746556616C696461746F72
gas_used: "2597883"
gas_wanted: "0"
height: "2271776"
info: ""
logs:
- events:
  - attributes:
    - key: receiver
      value: agoric1tygms3xhhs3yv487phx3dw4a95jn7t7lnxhpl4
    - key: amount
      value: 1000000ubld
    type: coin_received
  - attributes:
    - key: spender
      value: agoric1pmwdmxg75lt7ahd630r7lf7k5rx2ff8jmh8s9j
    - key: amount
      value: 1000000ubld
    type: coin_spent
  - attributes:
    - key: validator
      value: agoricvaloper1pmwdmxg75lt7ahd630r7lf7k5rx2ff8jt05een
    - key: amount
      value: 1000000ubld
    type: create_validator
  - attributes:
    - key: action
      value: /cosmos.staking.v1beta1.MsgCreateValidator
    - key: module
      value: staking
    - key: sender
      value: agoric1pmwdmxg75lt7ahd630r7lf7k5rx2ff8jmh8s9j
    type: message
  log: ""
  msg_index: 0
raw_log: '[{"events":[{"type":"coin_received","attributes":[{"key":"receiver","value":"agoric1tygms3xhhs3yv487phx3dw4a95jn7t7lnxhpl4"},{"key":"amount","value":"1000000ubld"}]},{"type":"coin_spent","attributes":[{"key":"spender","value":"agoric1pmwdmxg75lt7ahd630r7lf7k5rx2ff8jmh8s9j"},{"key":"amount","value":"1000000ubld"}]},{"type":"create_validator","attributes":[{"key":"validator","value":"agoricvaloper1pmwdmxg75lt7ahd630r7lf7k5rx2ff8jt05een"},{"key":"amount","value":"1000000ubld"}]},{"type":"message","attributes":[{"key":"action","value":"/cosmos.staking.v1beta1.MsgCreateValidator"},{"key":"module","value":"staking"},{"key":"sender","value":"agoric1pmwdmxg75lt7ahd630r7lf7k5rx2ff8jmh8s9j"}]}]}]'
timestamp: ""
tx: null
txhash: 53985A559B9205ECFE1795DEFACF8A7BB09C42AEAAD3A6801FBF73A42A84F72C
