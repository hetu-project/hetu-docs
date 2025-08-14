# Hetu Subnet

## Overview

Hetu is an open-source decentralized platform that enables participants to collaborate in producing high-quality digital commodities across various domains, including:

- **Compute Power** - Distributed computing resources
- **Artificial Intelligence** - AI inference and training services
- **On-chain scientific provenance** - Scientific computation for scientific research
- **Financial Market** - RWA and capital markets
- **And many more** - Extensible to various digital commodity types

## Architecture

Hetu is structured as a network of interconnected **subnets**, where each subnet operates as an autonomous community with its own specialized focus:

- **Independent Operation** - Each subnet functions as a self-governing ecosystem
- **Specialized Communities** - Subnets focus on specific types of digital commodities
- **Decentralized Governance** - Community-driven decision making within each subnet

## Network Participants

The Hetu network consists of four key participant types, each playing a crucial role in the ecosystem:

### Subnet Creators
- **Role**: Design and manage subnet-specific incentive mechanisms
- **Contribution**: Define work specifications and evaluation criteria
- **Responsibility**: Maintain fair and effective reward systems
- **Learn More**:
    - [Understanding Hetu Subnets](04-01-Understand-Subnet.md)
    - [Create a Subnet](04-09-Create-Subnet.md)

### Miners
- **Role**: Produce digital commodities and provide computational resources
- **Contribution**: Generate value through specialized work and services
- **Incentives**: Earn Hetu tokens (τ) based on the quality and value of their contributions
- **Learn More**: [Mining in Hetu](04-03-Mining.md)

### Validators
- **Role**: Evaluate and validate the quality of miners' work
- **Contribution**: Ensure network integrity by assessing output quality
- **Incentives**: Receive rewards for accurate validation work
- **Learn More**: [Validating in Hetu](04-04-Validating.md)


### Stakers
- **Role**: Support network security by staking Hetu tokens
- **Contribution**: Delegate tokens to trusted validators
- **Benefits**: Earn passive rewards while strengthening network security
- **Learn More**: [Staking](04-02-Staking-Delegation.md)

## Token Economics

The Hetu network operates on a sophisticated token economic model:

### Native Token
- **Token**: HETU
- **Total Supply**: 21,000,000 HETU
- **Block Generation**: 12 seconds per block (≈ 5 blocks/minute)

### Emission Model
- **Algorithm**: Logarithmic Decay Algorithm based on issuance ratio (LSED model)
- **Initial Reward**: 1 HETU per block
- **Halving Pattern**: Block rewards follow a `1/2ⁿ` decay pattern based on issuance ratio
- **Decay Cycles**: Rewards halve at 50%, 75%, 87.5%, etc. of total supply issuance

### Reward Allocation
- **Subnet Rewards**: Dynamic allocation based on subnet count (Logarithmic Subnet Incentive)
- **Main Network Rewards**:
  - Community pool: 2%
  - Validator commission: 5%
  - Validator rewards: 93% (distributed based on stake weight and delegation)

### Subnet Incentive Mechanism
- **Growth Model**: Subnet rewards increase logarithmically with the number of subnets
- **Scaling**: As the ecosystem grows, more rewards are directed to subnet activities
- **Upper Limit**: Maximum subnet reward ratio capped at 90% of total emissions

### Learn More
- [Detailed Economic Model](04-06-Economic-Model.md)
- [Governance](04-07-Governance.md)

## Stakework Consensus

Stakework Consensus is the core algorithm that powers Hetu Network's incentive distribution system, running in the Evmos environment.

### Key Features

- **Validator-Driven Evaluation**: Validators submit weight vectors to score the quality of miners' work
- **Stake-Weighted Consensus**: Higher stake validators have greater influence on consensus
- **Malicious Rating Protection**: Clipping and EMA mechanisms reduce impact of outlier ratings
- **Historical Reputation**: Bond system tracks validators' historical trust relationships
- **Fair Reward Distribution**: Combines immediate performance with historical reputation

### Consensus Process

The consensus mechanism operates in epochs, with each epoch triggered based on block height and subnet parameters:

1. **Data Collection**: Gather stake and weight data from all validators
2. **Activity Verification**: Identify active validators based on recent participation
3. **Consensus Calculation**: Determine weighted median consensus scores
4. **Weight Adjustment**: Clip extreme ratings to ensure consensus stability
5. **Bond Updates**: Update historical reputation using Exponential Moving Average
6. **Reward Distribution**: Allocate rewards based on incentive and dividend scores

### Incentive Alignment

The Stakework Consensus creates powerful incentives for network participants:

- **Validators**: Rewarded for consistent, accurate evaluations aligned with majority stake
- **Miners**: Motivated to improve performance to achieve higher consensus scores
- **Network**: Benefits from stable, predictable evaluation and reward mechanisms

### Learn More
- [Detailed Consensus Mechanism](04-08-Stakework-Consensus.md)

## How to Participate

### Getting Started

You can join the Hetu network in multiple ways:

1. **As a Subnet Creator** - Launch your own specialized subnet community
2. **As a Staker** - Support the network by delegating tokens to validators
3. **As a Miner** - Contribute computational resources and produce digital commodities
4. **As a Validator** - Join an existing subnet to evaluate miners' work
  


### Development Tools

The Hetu ecosystem provides comprehensive tooling for developers and participants:

- **[Hetu CLI](05-00-CLI.md)** - Command-line interface for network interaction
- **[Hetu SDK](05-01-SDK.md)** - Software development kit for building applications
