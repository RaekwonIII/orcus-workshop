---
title: Orcus EVM workshop
tags: EMV, Subsquid, Polkadot, Dotsama, Indexing, Astar, Orcus, Workshop
description: View the slide with "Slide Mode".
slideOptions:
  center: false
  transition: fade
  spotlight:
    enabled: false
---

<style>
    .left {
        text-align: left;
    }
    .smol {
        font-size: 24px;
    }
    .reveal pre {
        height: 100%;
        width: 100%;
    }
</style>

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/cover_bg.png" -->

<!-- ![](https://i.imgur.com/Z4Jt5vh.png) -->
# Indexing EVM logs on Astar

## To power Orcus Decentralized Finance DApp

Slides available here
https://hackmd.io/@RaekwonIII/Orcus-workshop

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

  
## Blockchain data problems

<!-- ![](https://i.imgur.com/2YUZ2RP.jpg =400x400) -->

<!-- ![image alt](https://media.giphy.com/media/GbH8vRmrNHdVZhouBt/giphy.gif ) -->

![](https://media.giphy.com/media/lPF1CyJXXcTZmUrP2J/giphy.gif)

Projects need custom data analysis for informed decision making

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Monolith vs modular (1)

![](https://i.imgur.com/PZqYqYs.png)

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Monolith vs modular (2)

![](https://i.imgur.com/3sIMFS5.png)

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Monolith vs modular (3)

## Offload heavy lifting to Archives

Note:

* Lower storage requirements, less computing power

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Monolith vs modular (4)

## Pre-indexed "queryable" historical data

Note:

* API updates => wait for re-sync
  * Acts like a "blockchain cache"

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Monolith vs modular (5)

## Faster than RPC calls

Note:

* Can be scaled, does not depend on the DApp
* Bonus: can use Archives and Processors like LEGOs

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Permissive SDK

## Unified Substrate and EVM indexing

Note:

Can track both Substrate Events/Extrinsics AND Evm data in the same API

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Permissive SDK

## Full control of database schema

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Permissive SDK

## External libs/APIs in your project

E.g. fetch prices from Coikgecko? ✅

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Permissive SDK

## Extensions via custom resolvers

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Built for devs

## Easy setup, local development

Note:

It's much easier to test, break things, start, stop the server

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Built for devs

## Fast sync times

Note:

Fast sync times, because the raw indexing is done by Archives
Build and deploy production-ready APIs within *hours*

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Built for devs

## Serverless deployment

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Built for devs

![](https://i.imgur.com/diDqPWP.jpg)

Note:

* Generate as much boilerplate possible
* Type-safety of TypeScript
* Focus on business logic
* No manual JSON parsing
* No manual chasing of Runtime upgrades

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Codegen

## From schema

```graphql=
type Owner @entity {
  id: ID!
  ownedTokens: [Token!]! @derivedFrom(field: "owner")
  balance: BigInt
}
```

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Codegen

## To Models

```typescript=
@Entity_()
export class Owner {
  constructor(props?: Partial<Owner>) {
    Object.assign(this, props)
  }

  @PrimaryColumn_()
  id!: string

  @OneToMany_(() => Token, e => e.owner)
  ownedTokens!: Token[]

  @Column_("numeric", {transformer: marshal.bigintTransformer, nullable: true})
  balance!: bigint | undefined | null
}
```

Note:

Easier to define entities as a schema, code is built automatically

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Typegen

## Type-safe wrappers for Substrate

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

## Runtime upgrades

![](https://i.imgur.com/1EPSqM7.png =470x)
![](https://i.imgur.com/AMm3Wud.png =470x)
![](https://i.imgur.com/w0LygwF.png =470x)

Note:

Runtime upgrades break things...

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

## 👋Runtime upgrade bugs

```typescript=
export class BalancesTransferEvent {
  constructor(private ctx: EventContext) {
    assert(this.ctx.event.name === 'balances.Transfer')
  }

  get isV49(): boolean {
    return this.ctx._chain.getEventHash('balances.Transfer') === 'dfcae516f053c47e7cb49e0718f01587efcb64cea4e3baf4c6973a29891f7841'
  }

  get asV49(): [Uint8Array, Uint8Array, bigint] {
    assert(this.isV49)
    return this.ctx._chain.decodeEvent(this.ctx.event)
  }

  get isV1201(): boolean {
    return this.ctx._chain.getEventHash('balances.Transfer') === '23222c59f2992c12387568241620899d2d399ab9027595daca8255637f62ece3'
  }

  get asV1201(): {from: v1201.AccountId20, to: v1201.AccountId20, amount: bigint} {
    assert(this.isV1201)
    return this.ctx._chain.decodeEvent(this.ctx.event)
  }
}
```

Note:

* Keep track of Runtime changes
* No manual procedure
* Reduce bugs

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# EVM Typegen

## Type-safe interfaces from ABI deserialization

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

## Type-safe interfaces from ABI deserialization

### From JSON...

```json=
[
    {
      "inputs": [
        {
          "internalType": "string",
          "name": "name",
          "type": "string"
        },
        {
          "internalType": "string",
          "name": "symbol",
          "type": "string"
        },
        {
          "internalType": "string",
          "name": "baseURI",
          "type": "string"
        }
      ],
      "stateMutability": "nonpayable",
      "type": "constructor"
    },
    {
      "anonymous": false,
      "inputs": [
        {
          "indexed": true,
          "internalType": "address",
          "name": "owner",
          "type": "address"
        },
        {
          "indexed": true,
          "internalType": "address",
          "name": "approved",
          "type": "address"
        },
        {
          "indexed": true,
          "internalType": "uint256",
          "name": "tokenId",
          "type": "uint256"
        }
      ],
      "name": "Approval",
      "type": "event"
    },
    {
      "anonymous": false,
      "inputs": [
        {
          "indexed": true,
          "internalType": "address",
          "name": "owner",
          "type": "address"
        },
        {
          "indexed": true,
          "internalType": "address",
          "name": "operator",
          "type": "address"
        },
        {
          "indexed": false,
          "internalType": "bool",
          "name": "approved",
          "type": "bool"
        }
      ],
      "name": "ApprovalForAll",
      "type": "event"
    },
    {
      "anonymous": false,
      "inputs": [
        {
          "indexed": true,
          "internalType": "address",
          "name": "from",
          "type": "address"
        },
        {
          "indexed": true,
          "internalType": "address",
          "name": "to",
          "type": "address"
        },
        {
          "indexed": true,
          "internalType": "uint256",
          "name": "tokenId",
          "type": "uint256"
        }
      ],
      "name": "Transfer",
      "type": "event"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "to",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "tokenId",
          "type": "uint256"
        }
      ],
      "name": "approve",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "owner",
          "type": "address"
        }
      ],
      "name": "balanceOf",
      "outputs": [
        {
          "internalType": "uint256",
          "name": "",
          "type": "uint256"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [],
      "name": "baseURI",
      "outputs": [
        {
          "internalType": "string",
          "name": "",
          "type": "string"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "tokenId",
          "type": "uint256"
        }
      ],
      "name": "getApproved",
      "outputs": [
        {
          "internalType": "address",
          "name": "",
          "type": "address"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "owner",
          "type": "address"
        },
        {
          "internalType": "address",
          "name": "operator",
          "type": "address"
        }
      ],
      "name": "isApprovedForAll",
      "outputs": [
        {
          "internalType": "bool",
          "name": "",
          "type": "bool"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [],
      "name": "name",
      "outputs": [
        {
          "internalType": "string",
          "name": "",
          "type": "string"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "tokenId",
          "type": "uint256"
        }
      ],
      "name": "ownerOf",
      "outputs": [
        {
          "internalType": "address",
          "name": "",
          "type": "address"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "from",
          "type": "address"
        },
        {
          "internalType": "address",
          "name": "to",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "tokenId",
          "type": "uint256"
        }
      ],
      "name": "safeTransferFrom",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "from",
          "type": "address"
        },
        {
          "internalType": "address",
          "name": "to",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "tokenId",
          "type": "uint256"
        },
        {
          "internalType": "bytes",
          "name": "_data",
          "type": "bytes"
        }
      ],
      "name": "safeTransferFrom",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "operator",
          "type": "address"
        },
        {
          "internalType": "bool",
          "name": "approved",
          "type": "bool"
        }
      ],
      "name": "setApprovalForAll",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "bytes4",
          "name": "interfaceId",
          "type": "bytes4"
        }
      ],
      "name": "supportsInterface",
      "outputs": [
        {
          "internalType": "bool",
          "name": "",
          "type": "bool"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [],
      "name": "symbol",
      "outputs": [
        {
          "internalType": "string",
          "name": "",
          "type": "string"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "index",
          "type": "uint256"
        }
      ],
      "name": "tokenByIndex",
      "outputs": [
        {
          "internalType": "uint256",
          "name": "",
          "type": "uint256"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "owner",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "index",
          "type": "uint256"
        }
      ],
      "name": "tokenOfOwnerByIndex",
      "outputs": [
        {
          "internalType": "uint256",
          "name": "",
          "type": "uint256"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "tokenId",
          "type": "uint256"
        }
      ],
      "name": "tokenURI",
      "outputs": [
        {
          "internalType": "string",
          "name": "",
          "type": "string"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [],
      "name": "totalSupply",
      "outputs": [
        {
          "internalType": "uint256",
          "name": "",
          "type": "uint256"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "from",
          "type": "address"
        },
        {
          "internalType": "address",
          "name": "to",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "tokenId",
          "type": "uint256"
        }
      ],
      "name": "transferFrom",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    }
  ]
```

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

## ABI automated parsing

### ...to TypeScript

```typescript=
export interface TransferAddressAddressUint256Event {
  from: string;
  to: string;
  tokenId: ethers.BigNumber;
}

export const events = {
  // ...
  "Transfer(address,address,uint256)":  {
    topic: abi.getEventTopic("Transfer(address,address,uint256)"),
    decode(data: EvmEvent): TransferAddressAddressUint256Event {
      const result = abi.decodeEventLog(
        abi.getEvent("Transfer(address,address,uint256)"),
        data.data || "",
        data.topics
      );
      return  {
        from: result[0],
        to: result[1],
        tokenId: result[2],
      }
    }
  },
}
```

Note:

* No manual JSON parsing
* Pass data through interfaces

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

## Resources

* [Subsquid Docs](https://docs.subsquid.io)
* [Template project](https://github.com/subsquid/squid-template)
* [Aquarium](https://app.subsquid.io/aquarium)
* [Stackexchange](https://substrate.stackexchange.com/) (subsquid tag):
* Community: [Telegram](https://t.me/HydraDevs) group, [Discord](https://discord.gg/dxR4wNgdjV) server 

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Let’s get coding…🦑

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

## Goal: Track Investment Deposits

1. Setup<!-- .element: class="fragment" -->
2. Review the schema<!-- .element: class="fragment" -->
3. Generate models<!-- .element: class="fragment" -->
4. Generate Interfaces<!-- .element: class="fragment" -->
5. Implement logic in Processor<!-- .element: class="fragment" -->
6. Launch database container<!-- .element: class="fragment" -->
7. Create and apply database migration<!-- .element: class="fragment" -->
8. Launch processor and GraphQL server<!-- .element: class="fragment" -->

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

<!-- .slide: class="smol" -->
<!-- .slide: class="left" -->

# Setup

* Requisites: [Node.js](https://nodejs.org/en/download/) (16 or later), [Docker](https://docs.docker.com/get-docker/)

* GitHub [template](https://github.com/subsquid/squid-evm-template), *Use this template*, then

```bash
git clone git@github.com:<account>/squid-evm-template.git
```

* Install dependencies from project's root folder

```bash
cd squid-template && npm i
```


---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

<!-- .slide: class="smol" -->
<!-- .slide: class="left" -->

# Codegen (schema)

Define entities we want to track in `schema.graphql` file in the project root folder

* Investment

```graphql=
type Investements @entity {
  id: ID!
  timestamp: BigInt!
  value: Float!
}

```

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

<!-- .slide: class="smol" -->
<!-- .slide: class="left" -->

# Codegen (proj structure)

* From project's root folder, launch 
```bash
npx sqd codegen  # or `make codegen`
```
* New files will be created ![](https://i.imgur.com/16izs2Y.png =x450)

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

<!-- .slide: class="smol" -->
<!-- .slide: class="left" -->

# Codegen (classes)

### Let's take the `Owner` entity as an example

```typescript=
import {Entity as Entity_, Column as Column_, PrimaryColumn as PrimaryColumn_} from "typeorm"
import * as marshal from "./marshal"

@Entity_()
export class Investement {
  constructor(props?: Partial<Investement>) {
    Object.assign(this, props)
  }

  @PrimaryColumn_()
  id!: string

  @Column_("numeric", {transformer: marshal.bigintTransformer, nullable: false})
  timestamp!: bigint

  @Column_("numeric", {nullable: false})
  value!: number
}

```

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

<!-- .slide: class="smol" -->
<!-- .slide: class="left" -->

# EVM Typegen

* We need the "bank safe" ABI to be able to process its logs
* Copy it from [here](https://blockscout.com/astar/address/0xd89dEa2daC8Fb73F4107C2cbeA5Eb36dab511F64/contracts)
* Create a `src/abi` folder to contain all ABIs
* Paste it into a file named `bank-safe.json` in `src/abi`
* From project root folder, launch
```bash
npx squid-evm-typegen --abi src/abi/bank-safe.json --output src/abi/bank-safe.ts
```

Note:
  https://github.com/R312r0/orcus-squid-evm/blob/main/src/abis/bank-safe.json

----

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

## Linting config

We may need to add these configurations to the `.eslint` file:

```typescript=
{
    "prettier/prettier": "off",
    "@typescript-eslint/no-unsafe-assignment": "off",
    "@typescript-eslint/no-unsafe-call": "off",
    "@typescript-eslint/no-unsafe-member-access": "off",
    "import/extensions": "off"
}
```

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

<!-- .slide: class="smol" -->
<!-- .slide: class="left" -->

# Processor logic

Processor logic needs to be updated, we maintain the handling of Substrate Events, but add EVM logs handling. Let's change the file like so:

```typescript=
import { EvmLogHandlerContext, SubstrateEvmProcessor } from "@subsquid/substrate-evm-processor";
import { lookupArchive } from "@subsquid/archive-registry";
import { events } from "./abi/bank-safe";
import { Investement } from "./model";

const processor = new SubstrateEvmProcessor("astar-substrate");

processor.setBatchSize(500);

processor.setDataSource({
  chain: "wss://astar.api.onfinality.io/public-ws",
  archive: lookupArchive("astar")[0].url,
});

processor.setTypesBundle("astar");
processor.addEvmLogHandler(
  "0xd89dEa2daC8Fb73F4107C2cbeA5Eb36dab511F64".toLowerCase(),
  {
      filter: [events["InvestDeposited(uint256)"].topic],
      range: {from: 864889}
  },
  bankSafeHandler
);

processor.run();

export async function bankSafeHandler(
  ctx: EvmLogHandlerContext
): Promise<void> {
  const investDeposit = events["InvestDeposited(uint256)"].decode(ctx);
  await ctx.store.save(
      new Investement({
          id: ctx.txHash,
          timestamp: BigInt(ctx.substrate.block.timestamp),
          value: (Number(investDeposit.amount) / 1e6)
      })
  )
}

```

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

<!-- .slide: class="smol" -->
<!-- .slide: class="left" -->

# Database

* Squid APIs need a database to store processed data
* Templates have `docker-compose.yml` file to launch a container
* From project's root folder, launch 
```bash
docker-compose up
```
* or, alternatively
```bash
make up
```

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

<!-- .slide: class="smol" -->
<!-- .slide: class="left" -->

# Create and apply database migration

* Build code

```bash=
npm run build
```

* Clean up existing migrations

```bash=
rm db/migrations/*.js
```

* Drop database (and create new one)

```bash=
sqd db drop
sqd db create
```

* Create and apply new migration

```bash=
sqd db create-migration Data # make migration
sqd db migrate # make migrate
```

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

<!-- .slide: class="smol" -->
<!-- .slide: class="left" -->

# Launch the project

The project consists of a Processor (which we just implemented) and a GraphQL server.

* Launch the Processor (this will lock the console window)

```bash=
node -r dotenv/config lib/processor.js # make process
```

* Launch the GraphQL server (in another console window)

```bash=
npx squid-graphql-server # make serve
```

* Open the browser at http://localhost:4350/graphql

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

## We got you, bruh!

![](https://media.giphy.com/media/3ov9jPkJaT4AgkB6Za/giphy.gif)

This project is available in our [Aquarium](https://app.subsquid.io/aquarium/Orcus-EVM-example)

The complete Orcus Finanance Squid is available in their [repo](https://github.com/R312r0/orcus-squid-evm)

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

## What's next?

* Get contract: https://blockscout.com/astar/
* Change `CHAIN_NODE` in `contract.ts` (`"wss://shiden.api.onfinality.io/public-ws"`)
* Change `archive` arg of `processor.setDataSource` in `processor.ts`
* Optional
  * change ABI
  * change EVM call signature

💣💣💣

---

<!-- .slide: data-background="https://github.com/RaekwonIII/orcus-workshop/raw/master/base_bg.png" -->

# Thank you 🦑


Follow the project on GitHub
https://github.com/subsquid/squid

![](https://media.giphy.com/media/qUIm5wu6LAAog/giphy.gif)

Give us a ⭐, would you?
