# Stakework Consensus

## Overview

Stakework Consensus is an on-chain algorithm module in the Hetu Network, similar to Bittensor Yuma Consensus, running in the Evmos environment. Its main task is to calculate the incentive distribution (emissions), dividends, and historical reputation (bonds) of each validator and node based on the evaluation of nodes (miners or other working nodes) by validators in the subnet.

The validators of each subnet will periodically submit a weight vector (weights) to score the work quality of other nodes. The Stakework consensus will calculate the final consensus score, incentive distribution, and reputation value by processing the weight matrix of all validators through the consensus algorithm and clipping.

Similar to the Yuma consensus, the Stakework consensus will:

1. Pay more attention to the scores of validators with higher stake and higher active level.
2. Reduce the impact of unreliable or malicious ratings through clipping and historical EMA (Exponential Moving Average) mechanisms.
3. Motivate validators to stay aligned with the consensus and continuously and stably participate in the evaluation.
4. Motivate nodes to improve performance, thereby achieving a higher comprehensive community score.

## Epoch Execution Process

### Overview

An epoch is a complete cycle of the Stakework consensus operation. Whether it is triggered is determined by the current block height b and the subnet parameter tempo:

$$(b+netuid+1) \mod (tempo+1) = 0$$

Where:
- **b**: Current block height
- **netuid**: Subnet ID
- **tempo**: The epoch cycle parameter of this subnet (the smaller, the more frequent)

When the above conditions are met, RunEpoch is executed once: read data → calculate consensus and pruning → update bonds → calculate dividends and incentives → distribute emission → persist state.

### Detailed Process

The consensus mechanism runs once per epoch, and the specific triggering conditions are determined by the `shouldRunEpoch` function, which makes judgments based on block height, subnet ID (`netuid`) and cycle (`tempo`). The entire consensus process can be divided into the following core steps:

1. **Get Data**: Collect the stake and weights data of all validators under a specific subnet.
2. **Active Level Check**: Based on the `activity_cutoff` parameter, mark validators that have not timed out since their last activity as Active.
3. **Staking Normalization**: Normalize the staking of active validators.
4. **Weighted Median Consensus**: Calculate the consensus score (Consensus) for each node, which is the weighted median of the weights reported by all validators.
5. **Weight Pruning**: Prune the weights of validators based on the consensus score and delta parameters to ensure they do not deviate too far from the consensus.
6. **Bond Calculation**: Based on the trimmed weights, the historical bonds are updated using the Exponential Moving Average (EMA) method for the calculation of the next epoch. Here, both fixed α and dynamic α modes are supported.
7. **Incentive and Dividend**: Calculate the incentive score (Incentive) for each node based on the trimmed weights and normalized staking; calculate the dividend score (Dividend) for each validator based on historical bonds.
8. **Reward Allocation**: Combine the incentive and dividend scores to proportionally distribute the total epoch reward (raoEmission) to each validator.
9. **Data Persistence**: Save the newly calculated bond data from this computation to storage for use in the next epoch.

![Stakework Consensus Process](./assets/Mermaid.png)

## Key Consensus Calculation Formula

The following are the most important calculation steps in the consensus mechanism and their corresponding mathematical formulas.

### 1. Normalized Stake

Normalized stake S<sub>norm</sub> refers to the proportion of the stake S<sub>i</sub> of each validator i to the total stake of all active validators. This ensures that only the stakes of active validators are considered when calculating the weighted median.

$$S_{norm,i}=\frac{S_i}{\sum_{k \in active} S_k}$$

Where `active` is the set of all active validators.

### 2. Weighted Median Consensus

The consensus score C<sub>j</sub> of each node j is the **weighted median** of the weights W<sub>ij</sub> reported by all validators i for it. This weighting is determined by the validator's normalized stake S<sub>norm</sub>.

Calculation process:
- For each node j, consider the weights W<sub>i,j</sub> reported by all its validators and the corresponding normalized staking S<sub>norm,i</sub> as a set of data pairs (W<sub>i,j</sub>, S<sub>norm,i</sub>).
- Sort these data pairs in ascending order of the weight W<sub>i,j</sub>.
- Then, find the smallest weight W<sub>i,j</sub> such that the cumulative normalized stake starting from the smallest weight exceeds κ times the total normalized stake.

$$C_j = W_{i,j} \text{ such that } \sum_{k \in sorted, k \leq i} S_{norm,k} \geq \kappa \cdot \sum_{k \in sorted} S_{norm,k}$$

Where κ is a parameter between 0 and 1, used to control the percentile of the median calculation.

### 3. Weight Clipping

Weight clipping is designed to prevent the weights reported by validators from being too extreme, thereby making the consensus more stable. The weight W<sub>ij</sub> reported by each validator i for node j will be clipped to an interval centered around the consensus score C<sub>j</sub> with a width of δ.

$$W'_{ij} = clamp(W_{ij}, C_j - \delta, C_j + \delta)$$

Where W'<sub>ij</sub> is the new weight after pruning, and δ is the pruning amplitude parameter. The clamp function limits the value to the range of [C<sub>j</sub>−δ, C<sub>j</sub>+δ].

### 4. Bonds Exponential Moving Average (EMA)

The bond B<sub>ij</sub> represents the historical trust of validator i in node j, which is calculated by performing an exponential Moving Average on the trimmed weight W'<sub>ij</sub>.

$$B_{ij}(t+1) = (1 - \alpha) \cdot B_{ij}(t) + \alpha \cdot W'_{ij}$$

Where B<sub>ij</sub>(t) is the bond from the previous epoch, and B<sub>ij</sub>(t+1) is the new bond of the current epoch. The α parameter controls the degree of influence of the new weights on historical bonds.

- **Fixed α**: When `liquidAlphaEnabled` is false, α equals a fixed parameter `bondsMovingAverage`.
- **Dynamic α**: When `liquidAlphaEnabled` is true, the value of α will be dynamically adjusted. α is affected by the difference between the validator's weight W<sub>ij</sub>, consensus score C<sub>j</sub>, and historical bond B<sub>ij</sub>. `alphaSigmoid` function is used to calculate dynamic α.

$$\alpha = \alpha_{low} + sigmoid(combinedDiff) \cdot (\alpha_{high} - \alpha_{low})$$

Here, `combinedDiff` represents the difference between validator i's weight for node j and its consensus score or historical bond.

### 5. Incentive and Dividends

These are the two main components of the final allocated reward.

- **Incentive**: The incentive score I<sub>j</sub> reflects the recognition that node j has received from all validators, and its calculation is the matrix multiplication of the trimmed weight matrix W' and the normalized staking vector S<sub>norm</sub>, followed by the application of the power function with parameter ρ.

$$I = Normalize((W' \cdot S_{norm})^{\rho})$$

- **Dividends**: The dividend score D<sub>i</sub> reflects the earnings that validator i has obtained from its historical bonds. The dividend of each validator is the sum of all its bonds.

$$D = Normalize(\sum_j B_{ij})$$

### 6. Emission Distribution

The final reward distribution is a combination of incentives and dividends. After normalizing the incentive and dividend vectors, the total epoch reward raoEmission is proportionally distributed.

$$Emission_i = (NormIncentive_i + NormDividends_i) \times raoEmission$$

## Summary of Incentive Mechanism

- Validators need to be consistent with the majority of stake to maintain or increase their bond (reputation value).
- Ratings deviating from the consensus will be constrained by clipping and have their weights reduced in bond updates.
- The incentive of the node depends both on the stable recognition (dividends) from validators and on the immediate scoring performance (incentive).
- Historical bonds smooth fluctuations and encourage stable, consistent behavior.