# Hetu Network Subnet Governance Model

## 1. Overview

The subnet governance model of Hetu Network is a distributed governance system based on token economics and staking mechanisms, aiming to create a decentralized network infrastructure. This model includes core links such as subnet registration, auction, management, and neuron (node) participation. It ensures the security, decentralization, and sustainable development of the network through economic incentives and governance mechanisms.

## 2. Core Components

### 2.1 Subnet Manager Contract (SubnetManager)

The subnet manager contract is the core of the governance model, responsible for subnet registration, activation, and management. Each subnet has its own Alpha token and AMM pool for liquidity management and value capture.

### 2.2 Neuron Manager Contract (NeuronManager)

The neuron manager contract is responsible for the registration, deregistration, and management of network nodes, including role assignment and permission management for validators and ordinary neurons.

### 2.3 Global Staking Contract (GlobalStaking)

The global staking contract manages users' HETU token staking, including global staking and subnet-specific staking allocation, providing economic guarantees for network participation.

### 2.4 Subnet AMM (SubnetAMM)

The Automated Market Maker contract provides a liquidity mechanism for each subnet, manages the exchange between HETU and Alpha tokens, and maintains a price discovery mechanism.

## 3. Subnet Registration and Auction Mechanism

### 3.1 Subnet Registration Process

1. **Registration Cost Calculation**:
   - Subnet registration requires locking a certain amount of HETU tokens
   - The locking cost is determined based on a dynamic auction mechanism, following the formula:
     
     $$lockCost = lastLock \times 2 - \frac{lastLock \times blocksPassed}{lockReductionInterval}$$
     
   - The minimum locking amount is `networkMinLock` (default 100 HETU)

2. **Registration Methods**:
   - Basic registration: Register a subnet with default hyperparameters
   - Custom registration: Register a subnet with some or all custom hyperparameters
   - Permit-based registration: Complete authorization and registration in a single transaction using the permit mechanism

3. **Registration Process**:
   - Calculate and collect the locking cost
   - Create the Alpha token for the subnet
   - Create the AMM pool for the subnet and inject initial liquidity
   - Record subnet information and assign netuid (subnet ID)
   - Set subnet hyperparameters

### 3.2 Auction Mechanism

Subnet registration adopts a dynamic auction mechanism with the following characteristics:

1. **Exponential Growth**: After each registration, the next registration cost doubles
2. **Time Decay**: Registration cost decreases linearly over time
3. **Minimum Threshold**: Maintain a minimum registration cost to ensure network accessibility
4. **Rate Limiting**: Limit registration frequency through the `networkRateLimit` parameter

Auction mechanism formula:

$$lockCost = \max(networkMinLock, lastLock \times 2 - \frac{lastLock \times blocksPassed}{lockReductionInterval})$$

## 4. Subnet Activation and Management

### 4.1 Subnet Activation

After creation, a subnet needs to be explicitly activated by the owner before it can accept neuron registrations:

```solidity
function activateSubnet(uint16 netuid) external nonReentrant;
```

### 4.2 Subnet Hyperparameters

Each subnet has a set of hyperparameters that define its operating rules:

1. **Network Parameters**:
   - `rho`: Consensus parameter
   - `kappa`: Incentive parameter
   - `immunityPeriod`: Immunity period
   - `tempo`: Network tempo
   - `maxValidators`: Maximum number of validators
   - `activityCutoff`: Activity threshold
   - `maxAllowedUids`: Maximum number of allowed neurons
   - `maxAllowedValidators`: Maximum number of allowed validators

2. **Economic Parameters**:
   - `baseNeuronCost`: Basic neuron registration cost
   - `validatorThreshold`: Minimum staking threshold for validators
   - `neuronThreshold`: Minimum staking threshold for neurons

3. **Governance Parameters**:
   - `registrationAllowed`: Whether registration is allowed
   - `commitRevealEnabled`: Whether commit-reveal mechanism is enabled
   - `commitRevealPeriod`: Commit-reveal period

### 4.3 Subnet Management Functions

Subnet owners and system administrators can perform the following management functions:

1. **Query Subnet Information**:
   - Get basic subnet information
   - Get subnet hyperparameters
   - Get detailed subnet information (including AMM pool status)

2. **System Management**:
   - Update network parameters (system administrators only)
   - Update subnet hyperparameters (system administrators only)
   - Reset network locking status (system administrators only)

## 5. Neuron Registration and Management

### 5.1 Neuron Registration

1. **Registration Prerequisites**:
   - The subnet must exist and be activated
   - The user must have sufficient available staking (meeting neuron or validator thresholds)
   - The subnet has not reached the maximum number of neurons or validators

2. **Registration Process**:
   ```solidity
   function registerNeuronWithStakeAllocation(
       uint16 netuid,
       uint256 stakeAmount,
       bool isValidatorRole,
       string calldata axonEndpoint,
       uint32 axonPort,
       string calldata prometheusEndpoint,
       uint32 prometheusPort
   ) external;
   ```

3. **Registration Costs**:
   - Basic registration cost: `baseNeuronCost`
   - Staking requirements: Ordinary neurons need to meet `neuronThreshold`, validators need to meet `validatorThreshold`

### 5.2 Neuron Roles

1. **Ordinary Neurons**:
   - Minimum staking requirement: `neuronThreshold`
   - Can participate in the network but do not validate transactions

2. **Validators**:
   - Minimum staking requirement: `validatorThreshold`
   - Responsible for transaction validation and consensus
   - Quantity limited to `maxAllowedValidators`

### 5.3 Neuron Management Functions

1. **Deregistration**:
   ```solidity
   function deregisterNeuron(uint16 netuid) external;
   ```

2. **Update Stake Allocation**:
   ```solidity
   function updateStakeAllocation(
       uint16 netuid,
       address account,
       uint256 newStake
   ) external;
   ```

3. **Update Service Information**:
   ```solidity
   function updateNeuronService(
       uint16 netuid,
       string calldata axonEndpoint,
       uint32 axonPort,
       string calldata prometheusEndpoint,
       uint32 prometheusPort
   ) external;
   ```

## 6. Staking Mechanism

### 6.1 Global Staking

1. **Add Global Stake**:
   ```solidity
   function addGlobalStake(uint256 amount) external;
   ```

2. **Remove Global Stake**:
   ```solidity
   function removeGlobalStake(uint256 amount) external;
   ```

### 6.2 Subnet Stake Allocation

1. **Allocate Stake to Subnet**:
   ```solidity
   function allocateToSubnet(uint16 netuid, uint256 amount) external;
   ```

2. **Deallocate from Subnet**:
   ```solidity
   function deallocateFromSubnet(uint16 netuid, uint256 amount) external;
   ```

### 6.3 Registration Costs

When registering a neuron, the system charges a basic registration cost:
```solidity
function chargeRegistrationCost(
    address user,
    uint16 netuid,
    uint256 cost
) internal;
```

These costs are transferred to the protocol treasury for network maintenance and development.

## 7. Liquidity and Price Discovery

### 7.1 AMM Pool Mechanism

Each subnet has a dedicated AMM pool that manages the exchange between HETU and Alpha tokens:

1. **Pool Initialization**:
   - AMM pool is created when the subnet is registered
   - Initial liquidity (equal value of HETU and Alpha tokens) is injected

2. **Exchange Mechanism**:
   - HETU to Alpha: `swapHETUForAlpha`
   - Alpha to HETU: `swapAlphaForHETU`

3. **Price Discovery**:
   - Dynamic price based on reserve ratio in the pool
   - Moving average price provides smooth price signals

### 7.2 Liquidity Management

1. **Inject Liquidity**:
   ```solidity
   function injectLiquidity(
       uint256 hetuAmount,
       uint256 alphaAmount
   ) external;
   ```

2. **Extract Liquidity**:
   ```solidity
   function withdrawLiquidity(
       uint256 hetuAmount,
       uint256 alphaAmount,
       address to
   ) external;
   ```

## 8. Adjustment of Governance Parameters

### 8.1 Network Parameters

System administrators can adjust the following network parameters:

1. **Minimum lock-up amount**: `networkMinLock`
2. **Rate limit**: `networkRateLimit`
3. **Lock reduction interval**: `lockReductionInterval`

### 8.2 Subnet Hyperparameters

System administrators can update the hyperparameters of subnets to adjust their operating rules:

```solidity
function updateSubnetHyperparams(
    uint16 netuid,
    SubnetTypes.SubnetHyperparams calldata newHyperparams
) external onlyAdmin;
```

## 9. Summary

Hetu Network's subnet governance model builds a scalable decentralized network infrastructure through economic incentives and staking mechanisms. The core features of this model include:

1. **Dynamic auction mechanism**: Ensures efficient allocation of subnet resources
2. **Dual-layer staking system**: Combines global staking and subnet-specific staking
3. **Role stratification**: Distinguishes between ordinary neurons and validator roles
4. **Liquidity mechanism**: Each subnet has an independent AMM pool and Alpha token
5. **Parameterized governance**: Achieves flexible governance through adjustable hyperparameters

This design enables Hetu Network to support a diverse ecosystem of subnets while maintaining the security and sustainable development of the overall network.