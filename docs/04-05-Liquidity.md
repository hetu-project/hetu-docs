# Liquidity in Hetu

## Overview

Hetu's Automated Market Maker (AMM) provides decentralized liquidity for trading between HETU tokens and subnet-specific Alpha tokens. The SubnetAMM contract implements a sophisticated mechanism that supports both stable and dynamic pricing models based on Subtensor architecture.

## AMM Architecture

### Core Components
- **HETU Token**: Network's native token used across all subnets
- **Alpha Token**: Subnet-specific tokens representing value within individual subnets
- **Dual Mechanism**: Supports both stable (1:1) and dynamic (AMM) pricing models
- **System-Controlled Liquidity**: Only authorized system addresses can inject/withdraw liquidity

### Reserve Structure
The AMM maintains three key reserves following Subtensor specifications:
- **Subnet HETU**: HETU tokens held in the pool
- **Subnet Alpha In**: Alpha tokens available for trading in the pool
- **Subnet Alpha Out**: Alpha tokens circulating outside the pool

## Mechanism Types

### Stable Mechanism
- **Fixed Exchange Rate**: 1:1 exchange between HETU and Alpha tokens
- **No Slippage**: Price remains constant regardless of trade size
- **Ideal for**: Subnets requiring price stability and predictable exchanges

### Dynamic Mechanism  
- **Constant Product Formula**: Uses AMM pricing based on k = HETU_reserve × Alpha_reserve
- **Market-Driven Pricing**: Price adjusts based on supply and demand
- **Price Impact**: Larger trades result in higher slippage
- **Ideal for**: Subnets with fluctuating token values and market dynamics

## Trading Functions

### HETU to Alpha Swaps
Users can exchange HETU tokens for subnet Alpha tokens:
- **Input**: HETU amount and minimum Alpha output required
- **Price Calculation**: Based on current reserves and mechanism type
- **Slippage Protection**: Transactions revert if output falls below minimum
- **Volume Tracking**: All trades contribute to user and total volume statistics

#### Exchange Formulas

**Stable Mechanism (1:1 Exchange)**
```
Alpha_out = HETU_in
```

**Dynamic Mechanism (AMM)**
```
k = subnetHETU × subnetAlphaIn (constant product)
new_subnetHETU = subnetHETU + HETU_in
new_subnetAlphaIn = k ÷ new_subnetHETU
Alpha_out = subnetAlphaIn - new_subnetAlphaIn
```

Where:
- `k`: Constant product maintained by the AMM
- `subnetHETU`: Current HETU reserve in pool  
- `subnetAlphaIn`: Current Alpha reserve available for trading
- `HETU_in`: Amount of HETU tokens being swapped
- `Alpha_out`: Amount of Alpha tokens received

### Alpha to HETU Swaps
Users can exchange Alpha tokens back to HETU:
- **Input**: Alpha amount and minimum HETU output required
- **Reverse Pricing**: Calculates HETU output based on Alpha input
- **Consistent Logic**: Uses same mechanism rules in reverse direction
- **Statistical Updates**: Maintains comprehensive trading records

#### Exchange Formulas

**Stable Mechanism (1:1 Exchange)**
```
HETU_out = Alpha_in
```

**Dynamic Mechanism (AMM)**
```
k = subnetHETU × subnetAlphaIn (constant product)
new_subnetAlphaIn = subnetAlphaIn + Alpha_in
new_subnetHETU = k ÷ new_subnetAlphaIn
HETU_out = subnetHETU - new_subnetHETU
```

Where:
- `Alpha_in`: Amount of Alpha tokens being swapped
- `HETU_out`: Amount of HETU tokens received
- Other variables same as above

## Liquidity Management

### System-Controlled Injection
Only authorized system addresses can add liquidity:
- **HETU Injection**: Adds HETU tokens to increase pool depth
- **Alpha Injection**: Adds Alpha tokens to balance the pool
- **Flexible Ratios**: Can inject tokens independently or together
- **Reserve Updates**: Automatically updates all reserve tracking

### Protected Withdrawal
System addresses can withdraw liquidity with safeguards:
- **Minimum Liquidity**: Ensures pool never falls below minimum thresholds
- **Balanced Withdrawal**: Can withdraw HETU, Alpha, or both
- **Destination Control**: Specifies recipient address for withdrawn tokens
- **Safety Checks**: Prevents pool depletion and maintains functionality

### Liquidity Protection
- **Minimum Pool Liquidity**: Configurable minimum reserve requirement
- **Withdrawal Limits**: Prevents complete liquidity drainage
- **Trading Continuity**: Ensures trades remain possible even during withdrawals

## Price Discovery and Tracking

### Current Price Calculation
- **Real-Time Pricing**: Calculated as (HETU_reserve × 1e18) / Alpha_reserve
- **Mechanism Dependent**: Stable mechanism maintains 1:1 ratio
- **Dynamic Updates**: Price changes with each trade in dynamic mechanism

#### Price Formulas

**Current Alpha Price**
```
Alpha_Price = (subnetHETU × 1e18) ÷ subnetAlphaIn
```

**Price After HETU→Alpha Swap**
```
new_Price = (new_subnetHETU × 1e18) ÷ new_subnetAlphaIn
```

**Price Impact Calculation**
```
Price_Impact = ((new_Price - old_Price) × 10000) ÷ old_Price
```
*Result in basis points (bps), where 100 bps = 1%*

### Moving Average Price
- **Exponential Moving Average**: Smooths price fluctuations over time
- **Block-Based Updates**: Updates based on blockchain block progression
- **Halving Time**: Configurable decay rate for historical price influence
- **Price Stability**: Provides reference for long-term value trends

#### Moving Average Formula
```
blocks_elapsed = current_block - last_update_block
alpha = (blocks_elapsed × 1e18) ÷ (blocks_elapsed + HALVING_TIME)
one_minus_alpha = 1e18 - alpha

moving_Price = (alpha × capped_current_price + one_minus_alpha × previous_moving_price) ÷ 1e18
```

Where:
- `HALVING_TIME = 1000 blocks` (configurable constant)
- `capped_current_price`: Current price capped at 1.0 (1e18)
- Prices are scaled by 1e18 for precision

### Price Impact Analysis
- **Trade Simulation**: Preview price impact before executing trades
- **Slippage Calculation**: Shows expected price movement for given trade size
- **Large Trade Warnings**: Alerts for trades significantly impacting price
- **Impact Thresholds**: Categorizes trades as normal, medium, large, or very large

## Trading Analysis and Statistics

### Volume Tracking
- **Total Volume**: Comprehensive tracking of all trading activity
- **User Volume**: Individual user trading history and statistics
- **Volume Percentage**: User's share of total trading volume
- **HETU-Denominated**: All volumes calculated in HETU equivalent value

### Pool Health Monitoring
- **Liquidity Status**: Real-time assessment of pool health
- **Reserve Ratios**: Balance between HETU and Alpha reserves
- **Health Indicators**: Clear status messages for pool conditions
- **Risk Assessment**: Early warning for low liquidity conditions

### Performance Metrics
- **Price History**: Tracks current and moving average prices
- **Trading Efficiency**: Measures successful vs failed trade attempts
- **Liquidity Utilization**: Shows how much of the pool is being used
- **Market Activity**: Indicates trading frequency and patterns

## Advanced Features

### Trade Preview System
- **Simulation Engine**: Test trades without executing them
- **Output Estimation**: Accurate prediction of received tokens
- **Price Impact Preview**: Shows how trade affects overall price
- **Liquidity Sufficiency**: Confirms sufficient reserves for trade

### Slippage Management
- **Dynamic Calculation**: Real-time slippage computation
- **Mechanism-Aware**: Different calculations for stable vs dynamic modes
- **Theoretical Comparison**: Shows difference from perfect pricing
- **User Protection**: Helps users set appropriate slippage tolerances

#### Slippage Calculation Formulas

**Theoretical Output (Perfect Pricing)**

For HETU→Alpha:
```
theoretical_Alpha_out = (HETU_in × subnetAlphaIn) ÷ subnetHETU
```

For Alpha→HETU:
```
theoretical_HETU_out = (Alpha_in × subnetHETU) ÷ subnetAlphaIn
```

**Slippage Rate**
```
slippage_rate = ((theoretical_out - actual_out) × 10000) ÷ theoretical_out
```
*Result in basis points (bps)*

**Slippage Examples:**
- 0-50 bps: Minimal slippage
- 50-200 bps: Low slippage  
- 200-500 bps: Moderate slippage
- 500+ bps: High slippage

Note: Stable mechanism has zero slippage (slippage_rate = 0)

### Reserve Verification
- **Consistency Checks**: Ensures internal accounting matches actual balances
- **Error Detection**: Identifies discrepancies in reserve tracking
- **Audit Support**: Provides transparency for pool operations
- **Security Monitoring**: Detects potential issues with token balances

## Integration Points

### Subnet Integration
- **Network ID**: Each AMM linked to specific subnet (netuid)
- **Creator Recording**: Tracks AMM creator for reference (no special privileges)
- **System Coordination**: Works with SubnetManager and other core contracts
- **Token Compatibility**: Seamlessly handles HETU and subnet Alpha tokens

### Factory Pattern
- **AMM Factory**: SubnetAMM instances created through factory contract
- **Standardization**: Consistent deployment parameters across all AMMs
- **Management**: Centralized tracking of all subnet AMM instances
- **Upgrades**: Coordinated updates through factory mechanism

## Security Features

### Access Control
- **System-Only Functions**: Critical operations restricted to system addresses
- **No Creator Privileges**: AMM creators have no special control after deployment
- **Immutable Parameters**: Core settings cannot be changed after deployment
- **Address Verification**: Built-in checks for authorized system addresses

### Economic Safety
- **Reentrancy Protection**: Prevents recursive call attacks
- **Overflow Prevention**: Safe math operations throughout contract
- **Zero Address Checks**: Validates all address parameters
- **Amount Validation**: Ensures positive values for all operations

### Operational Safeguards
- **Reserve Consistency**: Maintains accurate internal accounting
- **Minimum Liquidity**: Prevents pool depletion below operational thresholds
- **Trade Validation**: Comprehensive checks before executing swaps
- **Event Logging**: Complete audit trail of all operations

## Best Practices for Users

### Trading Strategy
- **Preview First**: Always use trade preview before executing large trades
- **Slippage Tolerance**: Set appropriate slippage limits based on mechanism type
- **Market Timing**: Consider moving average price for better entry/exit points
- **Volume Analysis**: Review your trading patterns and frequency

### Risk Management
- **Large Trade Warnings**: Pay attention to trade size impact warnings
- **Pool Health**: Check pool health before trading significant amounts
- **Price Impact**: Understand how your trade affects overall market price
- **Liquidity Assessment**: Ensure sufficient liquidity for your trading needs
