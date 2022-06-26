<h1><p align="center"><img alt="Antora-Banner" src="Antora-Labeled.png" /></p></h1>

<h1 align="center">Mainnet Antora</h1>

# Submit request for initial validator funding

- Fork this repo into your Github account

- Clone your repo using:

  ```bash
  git clone https://github.com/<YOUR-GITHUB-USERNAME>/Antora
  ```

- Create a new Markdown file in the `Grants` Directory

  ```bash
  cd Antora/Grants
  ```
- Name the Markdown file with your GITHUB username
- Include in the file your IDEP wallet address and a brief description why you should get a grant.

- Commit and push to your repo
- Create a PR onto https://github.com/IDEP-network/Antora
- Only PRs from individuals / groups with a history of successfully running validator nodes will be accepted. This is to ensure the network is successfully decentralized.


# Original launch docs

## Note these are left for historical purposes.

_Planned Start Time: JUNE 25 2022 at 6PM UTC._

### Block Explorer

https://chadscan.com/

### Minimum hardware requirements

- 8GB RAM
- 500GB of disk space
- 4v cpu amd64 CPU


### [Genesis File](/antora-1/genesis.json):

```bash
https://github.com/IDEP-network/Antora/blob/main/genesis.json
```

### Genesis sha256

```bash
sha256sum ~/.iond/config/genesis.json
....
```

### Seed node

[Full seed nodes list](/antora-1/seeds.txt).

```
6e52997400aaa1b3d2155e45cf2559bf7a4c5e76@164.92.161.91:26656
```

### Persistent Peers
```
f14e7dd78fd2462541f59eac08a8107fca89c2b3@75.119.159.159:26641
8ffc74dbcd5ab32bc89e058ec53060d5762f88b5@178.63.100.102:26656
2a5c7fb6475f4edf5ea36dd1d40aecc70f55fa45@65.108.106.19:11343
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
version: v1.0.0
commit: ""
go: go version go1.18.1 linux/amd64
```

### Full-Node Initialization
```
iond init <moniker> --chain-id Antora
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
wget https://raw.githubusercontent.com/IDEP-network/Antora/main/genesis.json
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
    --chain-id Antora
```

### Start your node
Now that everything is setup and ready to go, you can start your node.

```
iond start
```

### FAQ
#### Example of a command to create a Validator
```
iond tx staking create-validator --amount 15000000000000idep --from idep1d2nqcwf9zz9fx7xlesyt0gc3utfxe2mk6nfwey --pubkey idepvalconspub1zcjduepqztw5yzm5wj0yc600aaemxlmda5488jv9hycxfnta3w7vz9jgpawqc9qnhs --commission-rate 0.01 --commission-max-rate 0.05 --commission-max-change-rate 0.005 --min-self-delegation 1 --chain-id Antora
```
