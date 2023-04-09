# Substrate Tutorials Notes

## How to generate the keys:

### Account 1 -> password = roy
#### Sr25519
```sh
./target/release/node-template key generate --scheme Sr25519 --password-interactive
Key password:
Secret phrase:       grace bracket robust fork oven length cart gun cactus brisk range other
  Network ID:        substrate
  Secret seed:       0x6250deaea474a86a759800c02d7f1be648c251cc8a3cbf4c3e0ce3c1053a4942
  Public key (hex):  0xd2fac0e72a13e3ff31f94e38450c7eabb86591d03a3832f6f13176d0579bdc1e
  Account ID:        0xd2fac0e72a13e3ff31f94e38450c7eabb86591d03a3832f6f13176d0579bdc1e
  Public key (SS58): 5GqLQHcDJfvcwkrK9tYG62wf19tDygvzDyftmEF9Di2W3mGo
  SS58 Address:      5GqLQHcDJfvcwkrK9tYG62wf19tDygvzDyftmEF9Di2W3mGo
```

#### ED25519

```sh
./target/release/node-template key inspect --password-interactive --scheme Ed25519 "grace bracket robust fork oven length cart gun cactus brisk range other"
Key password:
Secret phrase:       grace bracket robust fork oven length cart gun cactus brisk range other
  Network ID:        substrate
  Secret seed:       0x6250deaea474a86a759800c02d7f1be648c251cc8a3cbf4c3e0ce3c1053a4942
  Public key (hex):  0x1cb1aa1bed6ba077e207ff49c9a8a420e636cc9fa93255ba064c5c57ae26da27
  Account ID:        0x1cb1aa1bed6ba077e207ff49c9a8a420e636cc9fa93255ba064c5c57ae26da27
  Public key (SS58): 5CiKyXCCMGzz9xhBZKn6NVAC6Ypq1Ao5ia6z9enU5nKa53NV
  SS58 Address:      5CiKyXCCMGzz9xhBZKn6NVAC6Ypq1Ao5ia6z9enU5nKa53NV
```

### Account 2 -> Password = roy

#### Sr25519

```sh
./target/release/node-template key generate --scheme Sr25519 --password-interactive
Key password:
Secret phrase:       photo robot buyer video tuition rail sweet avocado wink scorpion source vote
  Network ID:        substrate
  Secret seed:       0x375490ed1e294ccc66a922899fe314d90a8317c437eee1c000a6bfbff6d68bd2
  Public key (hex):  0xecbf7694539f441b7e307957549e96d6807182287423539ebebb7178a3bbd836
  Account ID:        0xecbf7694539f441b7e307957549e96d6807182287423539ebebb7178a3bbd836
  Public key (SS58): 5HR82wyokC6Re3yZpDwYq1CKkKbiRDXLS4rxEK2fTYB4yz77
  SS58 Address:      5HR82wyokC6Re3yZpDwYq1CKkKbiRDXLS4rxEK2fTYB4yz77
```

#### ED25519

```sh
./target/release/node-template key inspect --password-interactive --scheme Ed25519 "photo robot buyer video tuition rail sweet avocado wink scorpion source vote"
Key password:
Secret phrase:       photo robot buyer video tuition rail sweet avocado wink scorpion source vote
  Network ID:        substrate
  Secret seed:       0x375490ed1e294ccc66a922899fe314d90a8317c437eee1c000a6bfbff6d68bd2
  Public key (hex):  0xb91b9883a6805ad86fe2557ca9234d4ca40e288f448a1eb6c6ab99d07e3b164d
  Account ID:        0xb91b9883a6805ad86fe2557ca9234d4ca40e288f448a1eb6c6ab99d07e3b164d
  Public key (SS58): 5GFQuwDESdSoiEXnLFkSLzQobzCx8KWi4YApcCQCBpekVd9E
  SS58 Address:      5GFQuwDESdSoiEXnLFkSLzQobzCx8KWi4YApcCQCBpekVd9E
```

## Add validators

To add validators, I need to add and change the authority addresses in a chain specification by modifying the `aura` and `grandpa` sections.

1. `aura` section - Sr25519 SS58 Address
2. `grandpa` section - ED25519 SS58 Adress and a voting weight

Make sure to use unique keys for each validator. If two validators had the same keys, they produce conflicting blocks.

Convert specification json file into raw form:
```sh
./target/release/node-template build-spec --chain=customSpec.json --raw --disable-default-bootnode > customSpecRaw.json
```

## Run nodes

### Node 1

I need the same password that I used to create the keys.

```sh
./target/release/node-template \
  --base-path /tmp/node01 \
  --chain ./customSpecRaw.json \
  --port 30333 \
  --ws-port 9945 \
  --rpc-port 9933 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator \
  --rpc-methods Unsafe \
  --name MyNode01 \
  --password-interactive
```

#### Add keys to the keystore for `aura`

```sh
./target/release/node-template key insert --base-path /tmp/node01 \
  --chain customSpecRaw.json \
  --scheme Sr25519 \
  --suri "grace bracket robust fork oven length cart gun cactus brisk range other" \
  --password-interactive \
  --key-type aura
```

#### Add keys to the keystore for `grandpa`

```sh
./target/release/node-template key insert \
  --base-path /tmp/node01 \
  --chain customSpecRaw.json \
  --scheme Ed25519 \
  --suri "grace bracket robust fork oven length cart gun cactus brisk range other" \
  --password-interactive \
  --key-type gran
```

Verify that keys are in the keystore for node01

```sh
ls /tmp/node01/chains/local_testnet/keystore
```

### Node 2

```sh
./target/release/node-template \
  --base-path /tmp/node02 \
  --chain ./customSpecRaw.json \
  --port 30334 \
  --ws-port 9946 \
  --rpc-port 9934 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator \
  --rpc-methods Unsafe \
  --name MyNode02 \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWEp8H3vZ8xe734ERp6qp88SCvM1rW3SJMKmi9EKWAEUBP \
  --password-interactive
```

#### Add keys to the keystore for `aura`

```sh
./target/release/node-template key insert --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Sr25519 \
  --suri "photo robot buyer video tuition rail sweet avocado wink scorpion source vote" \
  --password-interactive \
  --key-type aura
```

#### Add keys to the keystore for `grandpa`

```sh
./target/release/node-template key insert --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Ed25519 --suri "photo robot buyer video tuition rail sweet avocado wink scorpion source vote" \
  --password-interactive \
  --key-type gran
```

Verify node 2

```sh
ls /tmp/node02/chains/local_testnet/keystore
```

