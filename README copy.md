# DeadManSwitch Subgraph

This is a subgraph for indexing the DeadManSwitchModule smart contract events.

## Overview

The subgraph indexes the following events:
- **CheckIn**: When the owner checks in to reset the timer
- **ClaimStarted**: When the beneficiary starts the claim process
- **ClaimCancelled**: When a claim is cancelled
- **ClaimFinalized**: When ownership is transferred to the beneficiary

## Prerequisites

- Node.js v16 or later
- Graph CLI: `npm install -g @graphprotocol/graph-cli`
- Access to a Graph Node (local or hosted service)

## Setup

1. Install dependencies:
```bash
cd thegraph
npm install
```

2. Update the contract address in `subgraph.yaml`:
   - Replace the `address` field with your deployed contract address
   - Update the `startBlock` to the block where your contract was deployed
   - Update the `network` if not using mainnet

3. Generate types from the ABI:
```bash
npm run codegen
```

4. Build the subgraph:
```bash
npm run build
```

## Deployment

### The Graph Studio (Recommended)

1. Create a subgraph in [The Graph Studio](https://thegraph.com/studio/)
2. Get your deploy key
3. Authenticate:
```bash
graph auth --studio <DEPLOY_KEY>
```
4. Deploy:
```bash
npm run deploy
```

### Local Graph Node

1. Start a local graph node (see [Graph Node docs](https://github.com/graphprotocol/graph-node))
2. Create the subgraph:
```bash
npm run create-local
```
3. Deploy:
```bash
npm run deploy-local
```

## Querying

Example queries:

### Get all Dead Man Switches
```graphql
{
  deadManSwitches {
    id
    safe
    beneficiary
    heartbeatInterval
    lastCheckIn
    claimReadyAt
    isClaimActive
  }
}
```

### Get recent check-ins
```graphql
{
  checkInEvents(first: 10, orderBy: timestamp, orderDirection: desc) {
    id
    timestamp
    deadManSwitch {
      safe
      beneficiary
    }
    txHash
  }
}
```

### Get active claims
```graphql
{
  deadManSwitches(where: { isClaimActive: true }) {
    id
    safe
    beneficiary
    claimReadyAt
    lastCheckIn
  }
}
```

### Get claim history for a specific contract
```graphql
{
  deadManSwitch(id: "0x...") {
    safe
    beneficiary
    claimStartedEvents {
      claimReadyAt
      timestamp
      txHash
    }
    claimFinalizedEvents {
      oldOwner
      newOwner
      timestamp
      txHash
    }
  }
}
```

## Schema

See `schema.graphql` for the complete schema definition.

## Development

To make changes:
1. Update `schema.graphql` for schema changes
2. Update `src/mapping.ts` for handler logic
3. Run `npm run codegen` to regenerate types
4. Run `npm run build` to build
5. Deploy with `npm run deploy`

## License

MIT
