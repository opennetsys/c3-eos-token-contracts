# C3 EOS Token Contracts

> [EOS](https://github.com/EOSIO/eos) token smart contracts for [C3](https://github.com/c3systems/c3-go)

NOTE: this is a work-in progress.

## Tutorial

This is a tutorial on how to run EOS node and launch the token contract

### Install EOS node

Install EOS node

```bash
# pull eos image
docker pull eosio/eos-dev

# run eos node
docker run --rm --name eosio -d -p 8888:8888 -p 9876:9876 -v /tmp/work:/work -v /tmp/eosio/data:/mnt/dev/data -v /tmp/eosio/config:/mnt/dev/config eosio/eos-dev  /bin/bash -c "nodeos -e -p eosio --plugin eosio::producer_plugin --plugin eosio::history_plugin --plugin eosio::chain_api_plugin --plugin eosio::history_api_plugin --plugin eosio::http_plugin -d /mnt/dev/data --config-dir /mnt/dev/config --http-server-address=0.0.0.0:8888 --access-control-allow-origin=* --contracts-console --http-validate-host=false"
```

In a different terminal window

```bash
# tail node logs
docker logs --follow eosio
```

Verify it's running http://localhost:8888/v1/chain/get_info

### Create a wallet

In a seperate tab, SSH into container

```bash
docker exec -it eosio bash
```

Create a wallet

```bash
$ cleos wallet create --to-console
Save password to use in the future to unlock this wallet.
Without password imported keys will not be retrievable.
"PW5KGv8iXyLEH35eqEGaJ9Jms5Y2W9bcxgdq7B75uS7BTC5daJpUo"
```

Unlock wallet to allow importing keys

```bash
cleos wallet unlock
```

Import eosio key ([more info](https://github.com/EOSIO/eos/issues/4154#issuecomment-397820824))

```bash
cleos wallet import --private-key 5HuQGCKyW1cGHDVuHdfLfDHx3QqBhhudHSGFu9NCMSGkg3e6dnk
```

### Wallet keys

Create a key pair

```bash
$ cleos create key --to-console
Private key: 5JPNbTzK7rCzeTTVNfKoE2pVZ1K92kyjgGBhNGtHHNgShyAmPEq
Public key: EOS7F458XBRBtJPxR5rxoTaiqE1sae2Tzyn1g9ZvNH5xYmW8qwwLy
```

Import key to wallet

```bash
cleos wallet import -n default --private-key 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
```

### Accounts

Create an account

```bash
cleos create account eosio bob EOS7F458XBRBtJPxR5rxoTaiqE1sae2Tzyn1g9ZvNH5xYmW8qwwLy
```

```bash
cleos create account eosio alice EOS7F458XBRBtJPxR5rxoTaiqE1sae2Tzyn1g9ZvNH5xYmW8qwwLy
```

Set the eosio master contract and sign it with the private key we imported

```bash
cleos set contract eosio contracts/eosio.bios -p eosio@active
```

List accounts

```bash
cleos get accounts EOS7F458XBRBtJPxR5rxoTaiqE1sae2Tzyn1g9ZvNH5xYmW8qwwLy
```

### Token

Create account to deploy token contract to

```
cleos create account eosio eosio.token EOS7F458XBRBtJPxR5rxoTaiqE1sae2Tzyn1g9ZvNH5xYmW8qwwLy
```

Deploy token contract

```bash
cleos set contract eosio.token contracts/eosio.token -p eosio.token@active
```

Create token (eosio.token owns the SYS namespace)

```bash
cleos push action eosio.token create '{"issuer":"eosio", "maximum_supply":"1000000000.0000 SYS"}' -p eosio.token@active
```

#### Token Transfers

Issue tokens to account 'bob'

```bash
cleos push action eosio.token issue '[ "bob", "100.0000 SYS", "for demo" ]' -p eosio@active
```

Bob transfers token to alice

```bash
cleos push action eosio.token transfer '[ "bob", "alice", "25.0000 SYS", "m" ]' -p bob@active
```

## License

[MIT](LICENSE)
