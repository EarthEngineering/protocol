# EARTH protocol

## Table of Contents

1. [Intro](#intro)
2. [Accounts](#accounts)
3. [Witness](#witness)
4. [Block](#block)
5. [BlockHeader](#blockheader)
6. [Transactions](#transactions)
   1. [AccountCreateContract](#accountcreatecontract)
   2. [AccountUpdateContract](#accountupdatecontract)
   3. [TransferContract](#transfercontract)
   4. [TransferAssetContract](#transferassetcontract)
   5. [VoteAssetContract](#voteassetcontract)
   6. [VoteWitnessContract](#votewitnesscontract)
   7. [WitnessCreateContract](#witnesscreatecontract)
   8. [WitnessUpdateContract](#witnessupdatecontract)
   9. [AssetIssueContract](#assetissuecontract)
   10. [ParticipateAssetIssueContract](#participateassetissuecontract)
   11. [DeployContract](#deploycontract)
   12. [Contract Types](#contract-types)
   13. [TXOutputs](#txoutputs)
   14. [TXOutput](#TXOutput)
   15. [TXInput](#TXInput)
   16. [Result](#Result)
7. [Inventory](#inventory)
8. [Items](#items)
9. [InventoryItems](#inventoryitems)
10. [BlockInventory](#blockinventory)
11. [BlockId](#blockid)
12. [ReasonCode](#reasoncode)
13. [DisconnectMessage](#disconnectmessage)
14. [HelloMessage](#hellomessage)
15. [Wallet Service RPC and Blockchain Explorer](#wallet-service-rpc-and-blockchain-explorer)
    1. [GetBalance](#getbalance)
    2. [CreateTransaction](#createtransaction)
    3. [BroadcastTransaction](#broadcasttransaction)
    4. [CreateAccount](#createaccount)
    5. [CreatAssetIssue](#creatassetissue)
    6. [ListAccounts](#listaccounts)
    7. [UpdateAccount](#updateaccount)
    8. [VoteWitnessAccount](#votewitnessaccount)
    9. [WitnessList](#witnesslist)
    10. [UpdateWitness](#updatewitness)
    11. [CreateWitness](#createwitness)
    12. [TransferAsset](#transferasset)
    13. [ParticipateAssetIssue](#participateassetissue)
    14. [ListNodes](#listnodes)
    15. [GetAssetIssueList](#getassetissuelist)
    16. [GetAssetIssueByAccount](#getassetissuebyaccount)
    17. [GetAssetIssueByName](#getassetissuebyname)
    18. [GetNowBlock](#getnowblock)
    19. [GetBlockByNum](#getblockbynum)
    20. [TotalTransaction](#totaltransaction)
16. [AccountList](#accountlist)
17. [WitnessList](#witnesslist)
18. [AssetIssueList](#assetissuelist)
19. [NodeList](#nodelist)
20. [Address](#address)
21. [Return](#return)
22. [Endpoint](#endpoint)
23. [PingMessage](#pingmessage)
24. [PongMessage](#pongmessage)
25. [FindNeighbours](#findneighbours)
26. [FindNeighbour](#findneighbour)
27. [Credit](#credit)

## Intro

- Defined by [Google Protobuf](https://developers.google.com/protocol-buffers)
- 3 types of account
  - basic account
  - asset release account
  - contract account
- Attributes included in each account
  - name
  - types
  - address
  - balance
  - related asset
- A basic account is able to apply to be a validation node, which has serval parameters including
  - extra attributes
  - public key
  - URL
  - voting statistics
  - history performance
  - and more...

## Accounts

There are three different `Account` types:

- `Normal`
- `AssetIssue`
- `Contract`

```protobuf
enum AccountType {
   Normal = 0;
   AssetIssue = 1;
   Contract = 2;
 }
```

An `Account` contains 7 parameters:

- `account_name`: the name for this account – e.g. “_GabrielsAccount_”.
- `type`: what type of this account is – e.g. _0_ stands for type `Normal`.
- `balance`: balance of this account – e.g. _4213312_.
- `votes`: received votes on this account – e.g. _{(“0x1b7w…9xj3”,323), (“0x8djq…j12m”,88),…,(“0x82nd…mx6i”,10001)}_.
- `asset`: other assets expect EARTH in this account – e.g. _{<“ExampleToken”,507>,<”OtherToken”,705>}_.
- `latest_operation_time`: the latest operation time of this account.

```protobuf
// Account
message Account {
  message Vote {
    bytes vote_address = 1;
    int64 vote_count = 2;
  }
  bytes accout_name = 1;
  AccountType type = 2;
  bytes address = 3;
  int64 balance = 4;
  repeated Vote votes = 5;
  map<string, int64> asset = 6;
  int64 latest_operation_time = 10;
}
```

## Witness

A `Witness` contains 8 parameters:

- `address`: the address of this witness – e.g. “_0xu82h…7237_”.
- `voteCount`: number of received votes on this witness – e.g. _234234_.
- `pubKey`: the public key for this witness – e.g. “_0xu82h…7237_”.
- `url`: the url for this witness – e.g. “_https://www.earth.engineering_”.
- `totalProduced`: the number of blocks this witness produced – e.g. _2434_.
- `totalMissed`: the number of blocks this witness missed – e.g. _7_.
- `latestBlockNum`: the latest height of block – e.g. _4522_.
- `isjobs`: a bool flag.

```protobuf
  // Witness
  message Witness{
    bytes address = 1;
    int64 voteCount = 2;
    bytes pubKey = 3;
    string url = 4;
    int64 totalProduced = 5;
    int64 totalMissed = 6;
    int64 latestBlockNum = 7;
    bool isJobs = 9;
  }
```

## Block

A block typically contains transaction data and a blockheader, which is a list of basic block information, including timestamp, signature, parent hash, root of Merkle tree and so on.

A block contains `transactions` and a `block_header`.

- `transactions`: transaction data of this block.
- `block_header`: one part of a block.

```protobuf
// block
message Block {
  repeated Transaction transactions = 1;
  BlockHeader block_header = 2;
 }
```

## BlockHeader

A `BlockHeader` contains `raw_data` and `witness_signature`.

- `raw_data`: a `raw` message.
- `witness_signature`: signature for this block header from witness node.

A message `raw` contains 6 parameters:

- `timestamp`: timestamp of this message – e.g. _14356325_.
- `txTrieRoot`: the root of Merkle Tree in this block – e.g. “_7dacsa…3ed_.”
- `parentHash`: the hash of last block – e.g. “_7dacsa…3ed_.”
- `number`: the height of this block – e.g. _13534657_.
- `witness_id`: the id of witness which packed this block – e.g. “_0xu82h…7237_”.
- `witness_address`: the adresss of the witness packed this block – e.g. “_0xu82h…7237_”.

```protobuf
message BlockHeader {
  message raw {
    int64 timestamp = 1;
    bytes txTrieRoot = 2;
    bytes parentHash = 3;
    //bytes nonce = 5;
    //bytes difficulty = 6;
    uint64 number = 7;
    uint64 witness_id = 8;
    bytes witness_address = 9;
  }
  raw raw_data = 1;
  bytes witness_signature = 2;
}
```

message `ChainInventory` contains `BlockId` and `remain_num`.

- `BlockId`: the identification of block.
- `remain_num`：the remain number of blocks in the synchronizing process.

A `BlockId` contains 2 parameters:

- `hash`: the hash of block.
- `number`: the hash and height of block.

```protobuf
message ChainInventory {
  message BlockId {
    bytes hash = 1;
    int64 number = 2;
  }
  repeated BlockId ids = 1;
  int64 remain_num = 2;
}
```

## Transaction

Transaction contracts mainly includes

- account create contract
- account update contract
- transfer contract
- transfer asset contract
- vote asset contract
- vote witness contract
- witness creation contract
- witness update contract
- asset issue contract
- participate asset issue contract
- deploy contract

### AccountCreateContract

An `AccountCreateContract` contains 3 parameters:

- `type`: What type this account is – e.g. _0_ stands for `Normal`.
- `account_name`: the name for this account – e.g.”_Gabrielsaccount_”.
- `owner_address`: the address of contract owner – e.g. “_0xu82h…7237_”.

```protobuf
message AccountCreateContract {
  AccountType type = 1;
  bytes account\*name = 2;
  bytes owner_address = 3;
}
```

### AccountUpdateContract

A `AccountUpdateContract` contains 2 paremeters:

- `account_name`: the name for this account – e.g.”\_Gabrielsaccount\*”.
- `owner_address`: the address of contract owner – e.g. “_0xu82h…7237_”.

```protobuf
message AccountUpdateContract {
  bytes account_name = 1;
  bytes owner_address = 2;
}
```

### TransferContract

A `TransferContract` contains 3 parameters:

- `amount`: the amount of EARTH – e.g. _12534_.
- `to_address`: the receiver address – e.g. “_0xu82h…7237_”.
- `owner_address`: the address of contract owner – e.g. “_0xu82h…7237_”.

```protobuf
message TransferContract {
  bytes owner_address = 1;
  bytes to_address = 2;
  int64 amount = 3;
}
```

### TransferAssetContract

A `TransferAssetContract` contains 4 parameters:

- `asset_name`: the name for asset – e.g.”_Gabrielsaccount_”.
- `to_address`: the receiver address – e.g. “_0xu82h…7237_”.
- `owner_address`: the address of contract owner – e.g. “_0xu82h…7237_”.
- `amount`: the amount of target asset - e.g._12353_.

```protobuf
message TransferAssetContract {
  bytes asset_name = 1;
  bytes owner_address = 2;
  bytes to_address = 3;
  int64 amount = 4;
 }
```

### VoteAssetContract

A `VoteAssetContract` contains 4 parameters:

- `vote_address`: the voted address of the asset.
- `support`: is the votes supportive or not – e.g. _true_.
- `owner_address`: the address of contract owner – e.g. “_0xu82h…7237_”.
- `count`: the count number of votes- e.g. _2324234_.

```protobuf
message VoteAssetContract {
  bytes owner_address = 1;
  repeated bytes vote_address = 2;
  bool support = 3;
  int32 count = 5;
 }
```

### VoteWitnessContract

A `VoteWitnessContract` contains 4 parameters:

- `vote_address`: the addresses of those who voted.
- `support`: is the votes supportive or not - e.g. _true_.
- `owner_address`: the address of contract owner – e.g. “_0xu82h…7237_”.
- `count`: - e.g. the count number of vote – e.g. _32632_.

```protobuf
message VoteWitnessContract {
  bytes owner_address = 1;
  repeated bytes vote_address = 2;
  bool support = 3;
  int32 count = 5;
 }
```

### WitnessCreateContract

A `WitnessCreateContract` contains 3 parameters:

- `private_key`: the private key of contract– e.g. “_0xu82h…7237_”.
- `owner_address`: the address of contract owner – e.g. “_0xu82h…7237_”.
- `url`: the url for the witness – e.g. “_https://www.earth.engineering_”.

```protobuf
message WitnessCreateContract {
  bytes owner_address = 1;
  bytes private_key = 2;
  bytes url = 12;
 }
```

### WitnessUpdateContract

A `WitnessUpdateContract` contains 2 parameters:

- `owner_address`: the address of contract owner – e.g. “_0xu82h…7237_”.
- `update_url`: the url for the witness – e.g. “_https://www.earth.engineering_”.

```protobuf
message WitnessUpdateContract {
   bytes owner_address = 1;
   bytes update_url = 12;
 }
```

### AssetIssueContract

An `AssetIssueContract` contains 11 parameters:

- `owner_address`: the address for contract owner – e.g. “_0xu82h…7237_”.
- `name`: the name for this contract – e.g. “Gabrielscontract”.
- `total_supply`: the maximum supply of this asset – e.g. _1000000000_.
- `earth_num`: the number of EARTH – e.g._232241_.
- `num`: number of corresponding asset.
- `start_time`: the starting date of this contract – e.g._20170312_.
- `end_time`: the expiring date of this contract – e.g. _20170512_.
- `vote_score`: the vote score of this contract received – e.g. _12343_.
- `description`: the description of this contract – e.g.”_earthdada_”.
- `url`: the url of this contract – e.g. “_https://www.earth.engineering_”.

```protobuf
message AssetIssueContract {
  bytes owner_address = 1;
  bytes name = 2;
  int64 total_supply = 4;
  int32 earth_num = 6;
  int32 num = 8;
  int64 start_time = 9;
  int64 end_time = 10;
  int32 vote_score = 16;
  bytes description = 20;
  bytes url = 21;
}
```

### ParticipateAssetIssueContract

A `ParticipateAssetIssueContract` contains 4 parameters:

- `owner_address`: the address for contract owner – e.g. “_0xu82h…7237_”.
- `to_address`: the receiver address – e.g. “_0xu82h…7237_”.
- `asset_name`: the name of target asset.
- `amount`: the amount of drops.

```protobuf
message ParticipateAssetIssueContract {
  bytes owner_address = 1;
  bytes to_address = 2;
  bytes asset_name = 3;
  int64 amount = 4;
}
```

### DeployContract

A `DeployContract` contains 2 parameters:

- `script`: the script of this contract.
- `owner_address`: the address for contract owner – e.g. “_0xu82h…7237_”.

```protobuf
message DeployContract {
  bytes owner_address = 1;
  bytes script = 2;
}                       t
```

Each transaction contains several TXInputs, TXOutputs and other related qualities. Input, transaction and head block all require signature.

message `Transaction` contains `raw_data` and `signature`.

- `raw_data`: message `raw`.
- `signature`: signatures form all input nodes.

`raw` contains 8 parameters:

- `type`: the transaction type of `raw` message.
- `vin`: input values.
- `vout`: output values.
- `expiration`: the expiration date of transaction – e.g._20170312_.
- `data`: data.
- `contract`: contracts in this transaction.
- `scripts`:scripts in the transaction.
- `timestamp`: timestamp of this raw data – e.g. _14356325_.

message `Contract` contains `type` and `parameter`.

- `type`: what type of the message contract.
- `parameter`: It can be any form.

### Contract Types

There are 8 different contract types:

- `AccountCreateContract`
- `TransferContract`
- `TransferAssetContract`
- `VoteAssetContract`
- `VoteWitnessContract`
- `WitnessCreateContract`
- `AssetIssueContract`
- `DeployContract`

`TransactionType` has two types:

- `UtxoType`
- `ContractType`

```protobuf
message Transaction {

  enum TranscationType {
    UtxoType = 0;
    ContractType = 1;
  }

  message Contract {
    enum ContractType {
      AccountCreateContract = 0;
      TransferContract = 1;
      TransferAssetContract = 2;
      VoteAssetContract = 3;
      VoteWitnessContract = 4;
      WitnessCreateContract = 5;
      AssetIssueContract = 6;
      DeployContract = 7;
      WitnessUpdateContract = 8;
      ParticipateAssetIssueContract = 9
     }
     ContractType type = 1;
     google.protobuf.Any parameter = 2;
  }

  message raw {
     TranscationType type = 2;
     repeated TXInput vin = 5;
     repeated TXOutput vout = 7;
     int64 expiration = 8;
     bytes data = 10;
     repeated Contract contract = 11;
     bytes scripts = 16;
     int64 timestamp = 17;
  }

  raw raw_data = 1;
  repeated bytes signature = 5;
}
```

### TXOutputs

message `TXOutputs` contains `outputs`.

- `outputs`: an array of `TXOutput`.

```protobuf
message TXOutputs {
   repeated TXOutput outputs = 1;
}
```

### TXOutput

message `TXOutput` contains `value` and `pubKeyHash`.

- `value`: output value.
- `pubKeyHash`: Hash of public key

```protobuf
message TXOutput {
   int64 value = 1;
   bytes pubKeyHash = 2;
}
```

### TXInput

message `TXInput` contains `raw_data` and `signature`.

- `raw_data`: a message `raw`.
- `signature`: signature for this `TXInput`.

message `raw` contains `txID`, `vout` and `pubKey`.

- `txID`: transaction ID.
- `vout`: value of last output.
- `pubKey`: public key.

```protobuf
message TXInput {
    message raw {
    bytes txID = 1;
    int64 vout = 2;
    bytes pubKey = 3;
  }
  raw raw_data = 1;
  bytes signature = 4;
}
```

### Result

message `Result` contains `fee` and `ret`.

- `ret`: the state of transaction.
- `fee`: the fee for transaction.

`code` is definition of `ret` and contains 2 types：

- `SUCCESS`
- `FAILED`

```protobuf
message Result {
  enum code {
    SUCESS = 0;
    FAILED = 1;
  }
  int64 fee = 1;
  code ret = 2;
}
```

## Inventory

Inventory is mainly used to inform peer nodes the list of items.

`Inventory` contains `type` and `ids`.

- `type`: what type this `Inventory` is. – e.g. _0_ stands for `EARTH`.
- `ids`: ID of things in this `Inventory`.

Two `Inventory` types: `EARTH` and `BLOCK`.

- `EARTH`: transaction.
- `BLOCK`: block.

```protobuf
// Inventory
message Inventory {
  enum InventoryType {
    EARTH = 0;
    BLOCK = 1;
   }
   InventoryType type = 1;
   repeated bytes ids = 2;
}
```

## Items

message `Items` contains 4 parameters:

- `type`: type of items – e.g. _1_ stands for `EARTH`.
- `blocks`: blocks in `Items` if there is any.
- `block_headers`: block headers if there is any.
- `transactions`: transactions if there is any.

`Items` have four types: `ERR`, `EARTH`, `BLOCK` and `BLOCKHEADER`.

- `ERR`: error.
- `EARTH`: transaction.
- `BLOCK`: block.
- `BLOCKHEADER`: block header.

```protobuf
message Items {
  enum ItemType {
    ERR = 0;
    EARTH = 1;
    BLOCK = 2;
    BLOCKHEADER = 3;
  }
  ItemType type = 1;
  repeated Block blocks = 2;
  repeated BlockHeader
  block_headers = 3;
  repeated Transaction transactions = 4;
}
```

## InventoryItems

`InventoryItems` contains `type` and `items`.

- `type`: what type of item.
- `items`: items in an `InventoryItems`.

```protobuf
message InventoryItems {
  int32 type = 1;
  repeated bytes items = 2;
}
```

## BlockInventory

message `BlockInventory` contains `type`.

- `type`: what type of inventory.

There are 3 types:

- `SYNC`
- `ADVTISE`
- `FETCH`

```protobuf
// Inventory
message BlockInventory {
  enum Type {
    SYNC = 0;
    ADVTISE = 1;
    FETCH = 2;
  }
}
```

## BlockId

message `BlockId` contains `ids` and `type`.

- `ids`: the identification of block.
- `type`: what type of the block.

`ids` contains 2 paremeters:

- `hash`: the hash of block.
- `number`: the hash and height of block.

```protobuf
message BlockId {
   bytes hash = 1;
   int64 number = 2;
}
  repeated BlockId ids = 1;
  Type type = 2;
}
```

## ReasonCode

`ReasonCode`: the type of reason.

`ReasonCode` contains 15 types of disconnect reasons:

- `REQUESTED`
- `TCP_ERROR`
- `BAD_PROTOCOL`
- `USELESS_PEER`
- `TOO_MANY_PEERS`
- `DUPLICATE_PEER`
- `INCOMPATIBLE_PROTOCOL`
- `NULL_IDENTITY`
- `PEER_QUITING`
- `UNEXPECTED_IDENTITY`
- `LOCAL_IDENTITY`
- `PING_TIMEOUT`
- `USER_REASON`
- `RESET`
- `UNKNOWN`

```protobuf
enum ReasonCode {
  REQUESTED = 0;
  TCP_ERROR = 1;
  BAD_PROTOCOL = 2;
  USELESS_PEER = 3;
  TOO_MANY_PEERS = 4;
  DUPLICATE_PEER = 5;
  INCOMPATIBLE_PROTOCOL = 6;
  NULL_IDENTITY = 7;
  PEER_QUITING = 8;
  UNEXPECTED_IDENTITY = 9;
  LOCAL_IDENTITY = 10;
  PING_TIMEOUT = 11;
  USER_REASON = 12;
  RESET = 16;
  UNKNOWN = 255;
}
```

## DisconnectMessage

message`DisconnectMessage` contains `reason`.

- `DisconnectMessage`: the message when disconnection occurs.
- `reason`: the reason for disconnecting.

## HelloMessage

message`HelloMessage` contains 2 parameters:

- `HelloMessage`: the message for building connection.
- `from`: the nodes that request for building connection.
- `version`: the version when connection is built.

## Wallet Service RPC and Blockchain Explorer

`Wallet` service contains several RPCs.

### `GetBalance`

Return balance of an `Account`.

### `CreateTransaction`

Create a transaction by giving a `TransferContract`. A Transaction containing a transaction creation will be returned.

### `BroadcastTransaction`

Broadcast a `Transaction`. A `Return` will be returned indicating if broadcast is success of not.

### `CreateAccount`

Create an account by giving a `AccountCreateContract`.

### `CreatAssetIssue`

Issue an asset by giving a `AssetIssueContract`.

### `ListAccounts`

Check out the list of accounts by giving a `ListAccounts`.

### `UpdateAccount`

Issue an asset by giving a `UpdateAccountContract`.

### `VoteWitnessAccount`

Issue an asset by giving a `VoteWitnessContract`.

### `WitnessList`

Check out the list of witnesses by giving a `WitnessList`.

### `UpdateWitness`

Issue an asset by giving a `WitnessUpdateContract`.

### `CreateWitness`

Issue an asset by giving a `WitnessCreateContract`.

### `TransferAsset`

Issue an asset by giving a `TransferAssetContract`.

### `ParticipateAssetIssue`

Issue an asset by giving a `ParticipateAssetIssueContract`.

### `ListNodes`

Check out the list of nodes by giving a `ListNodes`.

### `GetAssetIssueList`

Get the list of issue asset by giving a `GetAssetIssueList`.

### `GetAssetIssueByAccount`

Get issue asset by giving a `Account`.

### `GetAssetIssueByName`

Get issue asset by giving a`Name`.

### `GetNowBlock`

Get block.

### `GetBlockByNum`

Get block by block number.

### `TotalTransaction`

Check out the total transaction.

```protobuf
service Wallet {
  rpc GetAccount (Account) returns (Account) {};

  rpc CreateTransaction (TransferContract) returns (Transaction) {};

  rpc BroadcastTransaction (Transaction) returns (Return) {};

  rpc ListAccounts (EmptyMessage) returns (AccountList) {};

  rpc UpdateAccount (AccountUpdateContract) returns (Transaction) {};

  rpc CreateAccount (AccountCreateContract) returns (Transaction) {};

  rpc VoteWitnessAccount (VoteWitnessContract) returns (Transaction) {};

  rpc CreateAssetIssue (AssetIssueContract) returns (Transaction) {};

  rpc ListWitnesses (EmptyMessage) returns (WitnessList) {};

  rpc UpdateWitness (WitnessUpdateContract) returns (Transaction) {};

  rpc CreateWitness (WitnessCreateContract) returns (Transaction) {};

  rpc TransferAsset (TransferAssetContract) returns (Transaction) {}

  rpc ParticipateAssetIssue (ParticipateAssetIssueContract) returns (Transaction) {}

  rpc ListNodes (EmptyMessage) returns (NodeList) {}

  rpc GetAssetIssueList (EmptyMessage) returns (AssetIssueList) {}

  rpc GetAssetIssueByAccount (Account) returns (AssetIssueList) {}

  rpc GetAssetIssueByName (BytesMessage) returns (AssetIssueContract) {}

  rpc GetNowBlock (EmptyMessage) returns (Block) {}

  rpc GetBlockByNum (NumberMessage) returns (Block) {}

  rpc TotalTransaction (EmptyMessage) returns (NumberMessage) {}
};
```

## AccountList

`AccountList`: the list of acounts in the blockchain explorer.

message `AccountList` contains one parameter:

- `account`:

```protobuf
message AccountList {
  repeated Account accounts = 1;
}
```

## WitnessList

`WitnessList`: the list of witnesses in the blockchain explorer.

message `WitnessList` contains one parameter:

- `witnesses`:

```protobuf
message WitnessList {
  repeated Witness witnesses = 1;
}
```

## AssetIssueList

`AssetIssueList`: the list of issue asset in the blockchain explorer.

message `AssetIssueList` contains one parameter:

- `assetIssue`:

```protobuf
message AssetIssueList {
  repeated AssetIssueContract assetIssue = 1;
}
```

## NodeList

`NodeList`: the list of nodes in the node distribution map.

message `NodeList` contains one parameter:

- `nodes`:

```protobuf
message NodeList {
  repeated Node nodes = 1;
}
```

## Address

`Address`: the address of nodes.

message`Address` contains 2 parameters:

- `host`: the host of nodes.
- `port`: the port number of nodes.

```protobuf
message Address {
  bytes host = 1;
  int32 port = 2;
}
```

## Return

message `Return` has only one parameter:

- `result`: a bool flag.

```protobuf
message `Return` {
  bool result = 1;
}
```

The message structure of UDP.

## Endpoint

The storage structure of nodes' information.

message`Endpoint` contains 3 parameters:

- `address`: the address of nodes.
- `port`: the port number.
- `nodeId`:the ID of nodes.

```protobuf
message Endpoint {
   bytes address = 1;
   int32 port = 2;
   bytes nodeId = 3;
 }
```

## PingMessage

The message sent from one node to another in the connecting process.

message`PingMessage` contains 4 parameters:

- `from`: which node does the message send from.
- `to`: which node will the message send to.
- `version`: the version of the Internet.
- `timestamp`: the timestamp of message.

```protobuf
message PingMessage {
   Endpoint from = 1;
   Endpoint to = 2;
  int32 version = 3;
  int64 timestamp = 4;
}
```

## PongMessage

The message implies that nodes are connected.

message`PongMessage` contains 3 parameters:

- `from`: which node does the message send from.
- `echo`:
- `timestamp`: the timestamp of message.

```protobuf
message PongMessage {
  Endpoint from = 1;
  int32 echo = 2;
  int64 timestamp = 3;
}
```

## FindNeighbours

The message sent from one node to find another one.

message`FindNeighbours` contains 3 parameters:

- `from`: which node does the message send from.
- `targetId`: the ID of targeted node.
- `timestamp`: the timestamp of message.

```protobuf
message FindNeighbours {
  Endpoint from = 1;
  bytes targetId = 2;
  int64 timestamp = 3;
}
```

## FindNeighbour

The message replied by the neighbour node.

message`Neighbours` contains 3 parameters:

- `from`: which node does the message send from.
- `neighbours`: the neighbour node.
- `timestamp`: the timestamp of message.

```protobuf
message Neighbours {
  Endpoint from = 1;
  repeated Endpoint neighbours = 2;
  int64 timestamp = 3;
}
```

## Credit

Thank you to the [TRON team](https://tron.network) for the inspiration and [great code](https://github.com/tronprotocol/protocol/blob/master/English%20version%20of%20TRON%20Protocol%20document.md)!
