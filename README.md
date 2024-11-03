# Kopli Payment Salary

## Overview

The Kopli Payment Salary application seamlessly connects the Reactive Network with any EVM-compatible chain, enabling users to easily request funds from a faucet contract on a different blockchain. It’s as simple as sending an email—just a few clicks, and you can access the funds you need across multiple networks. Once the request is processed, **users will receive their funds directly in Reactive Kopli**, allowing them to interact with decentralized applications on the Reactive Network. The Reactive Network handles all the processing behind the scenes, ensuring a smooth and efficient experience. This streamlined process eliminates barriers and significantly enhances accessibility in the world of decentralized finance, making it easier for anyone to engage with blockchain technology.

## Contracts

- **Origin Chain Contract:** [ReactiveFaucetL1](https://github.com/Reactive-Network/kopli-faucet/blob/main/src/faucet/ReactiveFaucetL1.sol) handles Ether payment requests, defines a maximum payout per request, and emits `PaymentRequest` events containing details of the transaction.

- **Reactive Contract:** [ReactiveFaucet](https://github.com/Reactive-Network/kopli-faucet/blob/main/src/faucet/ReactiveFaucet.sol) operates on the Reactive Network. It subscribes to events on the Sepolia chain, processes callbacks, and distributes Ether to the appropriate receivers based on external `PaymentRequest` events.

## Deployment & Testing

To deploy the contracts to Ethereum Sepolia and Kopli Testnet, follow these steps. Replace the relevant keys, addresses, and endpoints as needed. Make sure the following environment variables are correctly configured before proceeding:

* `SEPOLIA_RPC` — https://rpc2.sepolia.org
* `SEPOLIA_PRIVATE_KEY` — Ethereum Sepolia private key
* `REACTIVE_RPC` — https://kopli-rpc.rkt.ink
* `REACTIVE_PRIVATE_KEY` — Kopli Testnet private key

### Step 1: Deploy ReactiveFaucetL1

Deploy the `ReactiveFaucetL1` contract to Ethereum Sepolia and assign the `Deployed to` address from the response to `REACTIVE_FAUCET_L1_ADDR`. You can skip this step and export the pre-deployed `REACTIVE_FAUCET_L1_ADDR` for Ethereum Sepolia shown above.

```bash
export REACTIVE_FAUCET_L1_ADDR=0x9b9BB25f1A81078C544C829c5EB7822d747Cf434
forge create --rpc-url $SEPOLIA_RPC --private-key $SEPOLIA_PRIVATE_KEY src/faucet/ReactiveFaucetL1.sol:ReactiveFaucetL1 --constructor-args 1ether
```

### Step 2: Deploy ReactiveFaucet
Deploy the ReactiveFaucet contract to the Reactive Network and assign the Deployed to address from the response to REACTIVE_FAUCET_ADDR.

```bash
forge create --rpc-url $REACTIVE_RPC --private-key $REACTIVE_PRIVATE_KEY src/faucet/ReactiveFaucet.sol:ReactiveFaucet --value 0.005ether --constructor-args $REACTIVE_FAUCET_L1_ADDR "10000000000000000"  # Setting max payout to 0.01 ETH (10 wei)
```

### Step 3: Check Balance
You can check the balance of the deployed Reactive Faucet contract to confirm that it has the correct funds.

```bash
cast balance --rpc-url $REACTIVE_RPC 0xd39c0D51cf44b75B6FE67dC6a979357095aDbc7A
```

### Step 4: Test the Faucet
Send a transaction to request funds from the faucet.

```bash
cast send $REACTIVE_FAUCET_L1_ADDR --rpc-url $SEPOLIA_RPC --private-key $SEPOLIA_PRIVATE_KEY --value 0.01ether
```