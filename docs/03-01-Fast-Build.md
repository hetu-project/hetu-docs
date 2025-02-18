
## **Summary**
Hetu Chain is a scalable, high-throughput blockchain that is fully compatible and interoperable with Ethereum.
It's built using the [Cosmos SDK](https://github.com/cosmos/cosmos-sdk/) and implements
[Narwhal](https://github.com/MystenLabs/narwhal) + [Bullshark](https://github.com/MystenLabs/sui/tree/main/consensus/bullshark) consensus mechanism
for improved performance and scalability.

## Hetu Chain

### Installation

For prerequisites and detailed build instructions
please read the [Installation](https://docs.hetu.org/protocol/hetu-cli) instructions.
Once the dependencies are installed, run:

```bash
make install
```

Or check out the latest [release](https://github.com/hetu-project/hetu/releases).

###  Deployment

**Important: Before deploying, ensure that the Narwhal mempool is deployed. See [hetu-parallel-engine/hetu-consensus](https://github.com/hetu-project/hetu-parallel-engine/tree/main/hetu-consensus) for details.**

#### Local Deployment

To deploy locally, use the `local_node.sh` script. This script will set up a local environment for running the Hetu Chain node.

```bash
./local_node.sh
```

#### Remote Deployment

For remote deployment, ensure that `hhubd` is available in the `PATH` on each machine. It is also recommended to set up SSH keys on the remote machines for secure and passwordless access.

1. `init_validators.sh`: This script initializes the validators required for the Hetu Chain. You need to provide the remote IPs for the 4 validators in the network as parameters.

   ```bash
   ./init_validators.sh <remote_ip1> <remote_ip2> <remote_ip3> <remote_ip4>
   ```

2. `start_node_archive.sh`: This script starts the node in archive mode.

   ```bash
   ./start_node_archive.sh
   ```

These scripts will help you set up and run the Hetu Chain on a remote server.