# Hetu Chain Subnet Creation Guide

This document details the complete process of creating and managing a Subnet on the Hetu Blockchain. Subnets are a key component of the Hetu ecosystem, allowing users to create dedicated computing networks and issue their own subnet tokens.

## Prerequisites

Before starting to create a subnet, ensure that:

1. The Hetu CLI tool (`hetucli`) has been installed
2. You have sufficient HETU tokens for staking and subnet creation
3. RPC connection to the Hetu Chain has been configured

## Step 1: Create or Import a Wallet

First, you need a wallet to execute transactions. You can either create a new wallet or import an existing private key:

```bash
# Create a new wallet
hetucli wallet create

# Or import an existing private key (recommended for testing)
hetucli w import <private-key> --name <wallet-name>
```

Example:
```bash
hetucli w import <your-private-key> --name mysubnet
```

## Step 2: Obtain WHETU Tokens

WHETU tokens are required to create a subnet. WHETU is a wrapped version of HETU, similar to the relationship between ETH and WETH.

1. Deposit HETU to get WHETU:

```bash
hetucli whetu deposit --sender <wallet-name> --value <amount>
```

Example: Deposit 1000 HETU:
```bash
hetucli whetu deposit --sender mysubnet --value 1000
```

2. Verify your WHETU balance:

```bash
hetucli whetu balance-of <wallet-name>
```

## Step 3: Check Network Lock Cost

Before creating a subnet, you need to check the current network lock cost:

```bash
hetucli subnet get-network-lock-cost
```

This will display the minimum amount of WHETU required to create a subnet.

## Step 4: Approve the Subnet Manager Contract to Use Your WHETU

You need to authorize the subnet manager contract to use your WHETU tokens:

```bash
hetucli whetu approve --spender <subnet_address> --value <amount> --sender <wallet-name>
```

Example:
```bash
hetucli whetu approve --spender 0x1234567890AbCdEf1234567890AbCdEf12345678 --value 100 --sender mysubnet
```

> Note: You can set the subnet manager contract address using the `hetucli c set subnet_address <address>` command. After this, you can omit the `--spender` parameter.

## Step 5: Update Network Parameters (Optional, Administrators Only)

If you are a network administrator, you may need to update network parameters:

```bash
hetucli subnet update-network-params \
  --network-min-lock <amount> \
  --network-rate-limit <limit> \
  --lock-reduction-interval <blocks> \
  --sender <wallet-name>
```

Example:
```bash
hetucli subnet update-network-params \
  --network-min-lock 100000000000000000000 \
  --network-rate-limit 1 \
  --lock-reduction-interval 10000 \
  --sender mysubnet
```

## Step 6: Register a New Subnet

Now you can register a new subnet:

```bash
hetucli subnet regist \
  --sender <wallet-name> \
  --name "<subnet-name>" \
  --description "<subnet-description>" \
  --token-name "<token-name>" \
  --token-symbol "<token-symbol>"
```

Example:
```bash
hetucli subnet regist \
  --sender mysubnet \
  --name "AI Vision" \
  --description "Computer vision and image processing network" \
  --token-name "VISION" \
  --token-symbol "VIS"
```

After successful registration, the system will assign a subnet ID (netuid) to your subnet.

## Step 7: Query Subnet Information

After registering the subnet, you can query subnet information:

```bash
# Query the total number of all subnets
hetucli subnet total-networks

# Query the next subnet ID to be assigned
hetucli subnet next-netuid

# Query detailed information of a specific subnet
hetucli subnet subnet-info --netuid <netuid>
```

Example:
```bash
hetucli subnet subnet-info --netuid 1
```

## Step 8: Stake and Participate in the Subnet

After creating the subnet, you may want to add staking to the subnet:

1. First, authorize the staking contract to use your WHETU:

```bash
hetucli whetu approve --spender <stake_address> --value <amount> --sender <wallet-name>
```

2. Add global staking:

```bash
hetucli stake add-stake --sender <wallet-name> --amount <amount>
```

3. Allocate staking to a specific subnet:

```bash
hetucli stake allocate-to-subnet --netuid <netuid> --sender <wallet-name> --amount <amount>
```

4. Register as a neuron (node) in the subnet:

```bash
hetucli neuron regist \
  --sender <wallet-name> \
  --netuid <netuid> \
  --is-validator-role \
  --axon-endpoint "<endpoint>" \
  --axon-port <port> \
  --prometheus-endpoint "<endpoint>" \
  --prometheus-port <port>
```

## Step 9: Manage the Subnet (Subnet Owner)

As the subnet owner, you can perform the following operations:

1. Update subnet information:

```bash
hetucli subnet update-subnet-info \
  --netuid <netuid> \
  --sender <wallet-name> \
  --new-name "<new-name>" \
  --new-description "<new-description>"
```

2. Activate or deactivate the subnet:

```bash
hetucli subnet activate-subnet --netuid <netuid> --sender <wallet-name>
```

## Step 10: Subnet Token Trading

Once the subnet is successfully created, you can trade subnet tokens via the AMM (Automated Market Maker):

1. Query subnet information and AMM pool information:

```bash
hetucli subnet subnet-info --netuid <netuid>
hetucli amm pool-info
```

2. Authorize the AMM contract to use your WHETU:

```bash
hetucli whetu approve --spender <amm_address> --value <amount> --sender <wallet-name>
```

3. Exchange HETU for subnet tokens:

```bash
hetucli amm swap-hetu-for-alpha \
  --hetu-amount-in <amount> \
  --alpha-amount-out-min 0 \
  --sender <wallet-name> \
  --to <to-address>
```

## Troubleshooting Common Issues

1. **Transaction Failure**: Ensure you have sufficient HETU to pay for gas fees and have authorized the contract to use enough WHETU.

2. **Insufficient Balance**: Use `hetucli wallet balance` and `hetucli whetu balance-of` to check your HETU and WHETU balances.

3. **Contract Address Not Set**: Use configuration commands to set necessary contract addresses:
   ```bash
   hetucli c set whetu_address <address>
   hetucli c set staking_address <address>
   hetucli c set subnet_address <address>
   hetucli c set neuron_address <address>
   hetucli c set amm_address <address>
   ```

## Summary

The process of creating a Hetu subnet includes preparing a wallet, obtaining WHETU tokens, authorizing contracts, registering the subnet, and subsequent staking and node registration. Through these steps, you can establish your own dedicated computing network in the Hetu ecosystem and issue your own tokens.

For more detailed information, refer to the [Hetu Official Documentation](https://github.com/hetu-project) or use the `hetucli --help` command to view available options.