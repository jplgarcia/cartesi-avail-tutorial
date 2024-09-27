# Developing with Cartesi and Avail: A Step-by-Step Guide

This guide will walk you through setting up a Cartesi dApp using Avail with two development modes: local and testnet. You will learn how to send transactions either directly or via Paio using EIP-712, and how to inspect the dApp state and outputs through NoNodo.

## Prerequisites
- **NoNodo**: A node responsible for communication with Avail and Cartesi.
- **Cartesi Machine**: Backend logic engine for the dApp.

### Install Tools:
1. **NoNodo**  
Install globally via npm (recommended):
```bash
npm i -g nonodo
```
Alternatively, install using Go:
```bash
go install github.com/calindra/nonodo@latest
export PATH="$HOME/go/bin:$PATH"
```

2. **Cartesi Machine**  
Download the [Cartesi Machine image](https://github.com/edubart/cartesi-machine-everywhere/releases) for your OS and add the `bin/` folder to your PATH:
```bash
export PATH="/path/to/cartesi-machine/bin:$PATH"
```


3. Optional, you can use the  **Cartesi CLI** to generate the base templates for the dApp
Install the Cartesi CLI globally:
```bash
npm i -g @cartesi/cli
```

## Development Modes:

### 1. Local Development
In this mode, everything runs locally without interacting with the Avail Testnet. NoNodo runs with default parameters, and a default dApp address is used.

Steps:
1. **Start NoNodo (Local Mode)**:
```bash
nonodo
```
2. **Create a Cartesi dApp**:
```bash
cartesi create my-dapp --template=python
cd my-dapp
cartesi build
```
3. **Run the Cartesi Machine Locally**:
```bash
cartesi-machine --network --flash-drive=label:root,filename:.cartesi/image.ext2 \
--volume=.:/mnt --env=ROLLUP_HTTP_SERVER_URL=http://10.0.2.2:5004 --workdir=/mnt -- python dapp.py
```

### 2. Testnet Development
Here, you interact with Avail Testnet, sending transactions to the dApp via Paio.

Steps:
1. **Start NoNodo (Testnet Mode)**:

2. **Set Up the dApp Address**:

3. **Create and Build Your dApp**:
Same as in local development:
```bash
cartesi create my-dapp --template=python
cd my-dapp
cartesi build
```

### Submit Transactions (Common for Both Modes)

The frontend or CLI will send input either either:
- Directly to the Input box which is deployed on the base layer.
- To Paio using EIP-712, which sequences and batches the transactions. In testnet mode, the transcations will in fact be delivered to Avail, while in local mode they will be sent directly to nonodo to simulate Sequenced transactions arriving in the DA.

TODO: Specify the utility of the `/submit` endpoint 

For local development, the frontend uses a different chain ID (local vs Sepolia).
TODO: Add IDs

TODO: Example of how to send inputs through CLI, from JS frontend to both Rollups Framework contracts and Paio

In the Avail network, the namespace for any dApp you will create is the "Cartesi" namespace, used by Paio and known by NoNodo.
The dApp address has to be included as part of the EIP-712 payload, this address is used by NoNodo to filter transactions in Paio.

## Interacting with Outputs:
- NoNodo provides a GraphQL API for inspecting the dApp state, outputs, notices, and reports. To understand outputs refer to our [docs](https://docs.cartesi.io/cartesi-rollups/1.5/rollups-apis/backend/introduction/).


## Understanding the Components
### NoNodo
NoNodo is the core infrastructure component that orchestrates the interaction between the Cartesi dApp and Avail. It functions as a Cartesi Rollups node and is responsible for managing inputs and outputs, whether locally or in the testnet environment. It fetches data from Avail by listening to the Cartesi namespace in Avail through Avail Reader. 

NoNodo contains the Cartesi Rollups API, enabling the dApp to process inputs, generate outputs, and handle state. It also offers a GraphQL API to read the dApp's outputs.

### Avail Reader
The Avail Reader is a component inside NoNodo responsible for reading data (transactions) from the Avail network. It allows the Cartesi dApp to process data from the testnet by fetching it from Avail.

### Paio
Paio acts as a bundler for inputs (transactions) sent to the dApp through the DA. It receives inputs from clients, organizes them, and submits them to the Avail network in batches. Paio takes in transactions via EIP-712 signed payloads from clients, organizes transactions into batches to be sent to Avail ensuring that transactions are submitted properly to the Avail network, allowing the dApp to process them in an orderly manner.

### InputBox and Portals Contracts
The InputBox and Portals Contracts are parts of the Cartesi Rollups Framework. They are designed for sending transactions or inputs to the Cartesi dApp. Both of these are pre-deployed to the base layer and work for both local and testnet environments as the destination of regular transactions and depositing assets.

### Cartesi Machine  
The Cartesi Machine is the emulator running in the background that executes the backend logic of the dApp. It works togetgher with NoNodo to process dApp logic given its current state and inputs. The CM Interacts with the Rollups API to handle state changes, process transactions, and produce outputs (such as vouchers, notices, and reports).
