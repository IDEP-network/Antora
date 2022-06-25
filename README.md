<h1><p align="center"><img alt="Antora-Banner" src="Antora-Labeled.png" /></p></h1>

<h1 align="center">Mainnet Antora</h1>

_Planned Start Time: JUNE 25 2022 at 6PM UTC._


### Minimum hardware requirements

- 8GB RAM
- 500GB of disk space
- 4v cpu amd64 CPU


### [Genesis File](/antora-1/genesis.json):

```bash
   .
```

### Genesis sha256

```bash
sha256sum ~/.iond/config/genesis.json
....
```

### Seed node

[Full seed nodes list](/antora-1/seeds.txt).

```
.
```

## Setup

**Prerequisites:** Make sure to have [Golang >=1.17](https://golang.org/).

### Full-Node Setup
- Download the IDEP client binary iond
```
git clone https://github.com/IDEP-network/Antora.git
```

- Add permissions to the binary
```
sudo chmod +x Antora/iond
```
- Move/Copy the binary to /usr/local/bin/
```
cp Antora/iond /usr/local/bin/
```

- Check the binary commands with
```
iond -h
```

### Verify Your Installation
Verify that everything is OK. If you get something like the following, you've successfully installed iond on your system.

```bash
$ iond version --long
name: Ion
server_name: iond
version: .
commit: .
```

### Full-Node Initialization
```
iond init <moniker> --chain-id Antora-Network
iond keys add <accountname>
```
- Save the mnemonic
- Find then add your IP address in the external_address field in the config.toml file
```
external_address=$(curl -s ifconfig.me):26656
sed -i.bak -e "s/^external_address = \"\"/external_address = \"$external_address\"/" $HOME/.ion/config/config.toml
```
- Next make your way to the nodes config directory, remove the genesis.json and replace it with the one provided in this repo
```
cd ~/.ion/config/

rm genesis.json
**************
```

### Setup Service and start the node
```
export USERNAME=$(whoami)
sudo -E bash -c 'cat << EOF > /etc/systemd/system/iond.service
[Unit]
Description=Iond Daemon
After=network-online.target

[Service]
User=$USERNAME
ExecStart=/home/$USERNAME/go/bin/iond start
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF'
```

```
sudo systemctl enable iond.service
sudo systemctl start iond.service
```


### Validator-Setup
- Once the Full Node is up and running you can fund your wallet through the faucet https://faucet.idep.network/

- To get your Public Address
```
iond keys list
```
- Create a Validator

Before you can create your Validator, your node has to be synced up to the latest block of the chain. You can check this by:

```
iond status
```
If `catching_up` is `false` you are good to execute:

```
iond tx staking create-validator \
    --amount 10000000000idep \
    --commission-max-change-rate 0.01 \
    --commission-max-rate 0.2 \
    --commission-rate 0.1 \
    --from <YourWalletAddress> \
    --min-self-delegation 1 \
    --moniker <YourMoniker> \
    --pubkey $(iond tendermint show-validator) \
    --chain-id Antora-Network
```

### Start your node
Now that everything is setup and ready to go, you can start your node.

```
iond start
```

### FAQ
#### Example of a command to create a Validator
```
iond tx staking create-validator --amount 15000000000000idep --from idep1d2nqcwf9zz9fx7xlesyt0gc3utfxe2mk6nfwey --pubkey idepvalconspub1zcjduepqztw5yzm5wj0yc600aaemxlmda5488jv9hycxfnta3w7vz9jgpawqc9qnhs --commission-rate 0.01 --commission-max-rate 0.05 --commission-max-change-rate 0.005 --min-self-delegation 1 --chain-id Antora-Network
```
