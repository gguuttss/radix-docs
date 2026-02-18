---
title: "Transaction Costing"
---
# Transaction Costing

## What are transaction costs?

At the very high-level, transaction costs can be broken down into the following categories:

|   
Item | Unit of Measurement | Description |
| --- | --- | --- |
| Execution | Execution Cost Unit | This is to account for the CPU usage during the execution of a transaction.
  

In this context, execution refers to the process of determining the state changes and other side effects by interpreting the instructions one-by-one upon some existing world state.

 |
| Finalisation | Finalisation Cost Unit | This is to account for the CPU usage during the finalisation of a transaction.  
  
In this context, finalisation refers to the process of committing state changes derived from a transaction and other auxiliary indices. |
| Storage | Bytes | This is to account for the additional data being added to a Node database.  
  
There are currently two type of storage costs:

1.  State Storage - The substates
2.  Archive Storage - Transaction payload, events and logs

 |
| Royalties | XRD and USD | The amount of XRD paid to blueprint developers and component owners for the use of the code or component instances. |
| Tip | Percentage | The extra amount of XRD paid to validators for the processing of a transaction.  
  
This is designed to help prioritise transaction when there is a traffic jam. |

## How does costing work?

![](../../../.gitbook/assets/Untitled.drawio.png)Transaction costing is done through the costing module within the System.

This module is responsible for tracking the fee balance, counting execution and finalisation cost units and applying the costs listed above, with the help of a [fee reserve](https://github.com/radixdlt/radixdlt-scrypto/blob/6d35fe85de69d82b85700aaa9a68310a6163b72e/radix-engine/src/system/system_modules/costing/fee_reserve.rs#L98-L143).

At the beginning of a transaction, the fee reserve is provided with a loan of XRD to bootstrap transaction execution. 

-   The amount is defined as:

```rust
execution_cost_unit_price  * ( 1 + tip_percentage / 100) * execution_cost_unit_loan
```

-   This loan must be repaid before `execution_cost_unit_loan` number of execution cost units are consumed ("repay condition"), otherwise the transaction is rejected.

After that, transaction execution starts, during which two processes happen

-   Kernel and system sends **execution cost** events (such as CPU usage, royalties) to the costing module:
    -   To deduct the fee balance
    -   To increase the cost unit counter
    -   To repay system loan and apply deferred costs, if **repay condition** is met
-   System sends vault lock fee events to the costing module:
    -   To credit fee balance

Once execution is done, costing module is instructed to apply **finalisation cost** and **storage cost**.

## Costing Parameters

The following parameters are used by the costing module.

### Protocol defined parameters

| Name | Value | Description |
| --- | --- | --- |
| `execution_cost_unit_price` | 0.00000005 | The price of execution cost unit in XRD. |
| `execution_cost_unit_limit` | 100,000,000 | The maximum number of execution cost units that a transaction can consume. |
| `execution_cost_unit_loan` | 4,000,000 | The number of execution cost units loaned from the system, to bootstrap transaction execution. |
| `finalization_cost_unit_price` | 0.00000005 | The price of finalisation cost unit in XRD.  |
| `finalization_cost_unit_limit` | 50,000,000 | The maximum number of finalisation cost units that a transaction can consume.  |
| `usd_price` | 16.666666666666666666 | The price of USD in XRD.  1 XRD = 0.06 USD |
| `state_storage_price` | 0.00009536743 | The price of state storage. 1 MiB = 6 USD |
| `archive_storage_price` | 0.00009536743 | The price of archive storage.  1MiB = 6 USD |

### Transaction defined parameters

| Name | Description |
| --- | --- |
| `tip_percentage` | The tip percentage specified by the transaction. |
| `free_credit_in_xrd` | The free credit amount specified by a preview request. |

## Fee Table

The table below further defines the cost of each costing entry.

| Category | Entry | Description | Cost |
| --- | --- | --- | --- |
| Execution  
(execution cost unit)
 | 

VerifyTxSignatures

 | Verify transaction signature. | Variable: `num_of_signature * 7000` |
| 

ValidateTxPayload

 | Verify transaction payload. | Variable: `size(payload) * 40` |
| 

RunNativeCode

 | Run native code. | Native code execution is billed in native execution unit per function based on table [here](https://github.com/radixdlt/radixdlt-scrypto/blob/main/radix-engine/assets/native_function_base_costs.csv).  
`34 native execution units = 1 execution cost unit` |
| 

RunWasmCode

 | Run WASM code. | WASM code execution is billed in WASM execution units per instruction based on weights [here](https://github.com/radixdlt/radixdlt-scrypto/blob/main/radix-engine/src/vm/wasm/weights.rs).  
`3000 WASM execution units = 1 execution cost unit` |
| 

PrepareWasmCode

 | Prepare WASM code. | Variable: `size(wasm) * 2`  
 |
| 

BeforeInvoke

 | Before function invocation. | Variable: `size(input) * 2`  
 |
| 

AfterInvoke

 | After function invocation. | Variable: `size(output) * 2`  
 |
| 

AllocateNodeId

 | Allocate a new node id. Node is the lower level representation of an object or key value store. | Fixed: `97` |
| 

CreateNode

 | Create a new node. | 

Variable: `size(substate) + 456`  
  

 |
| 

DropNode

 | Drop a node. | Variable: `size(substate) +1143`  
 |
| 

PinNode

 | Pin a node to a device (heap or track). | Variable: `12 + IO access` |
| 

MoveModule

 | Move module from one node to another. | Variable: `140 + IO access` |
| 

OpenSubstate

 | Open a substate of a node. | Variable: `303 + IO access`  |
| 

ReadSubstate

 | Read the value of a substate. | 

Variable:

-   If from heap: `65 + size(substate) * 2 + IO access`
-   If from track: `113 + size(substate) * 2 + IO access`

 |
| 

WriteSubstate

 | Update the value of a substate. | Variable: `218 + size(substate) * 2 + IO access` |
| 

CloseSubstate

 | Cloes a substate. | Fixed: `129` |
| 

MarkSubstateAsTransient

 | Marks a substate a transient. Transient substates are not committed after transaction. | Fixed: `55` |
| 

SetSubstate

 | Set the value of a substate | Variable: `133 + size(substate) * 2 + IO access` |
| 

RemoveSubstate

 | Remove a substate | Variable: `717 + IO access` |
| 

ScanKeys

 | Scan substate keys in a collection | Variable: `498 + IO access` |
| 

ScanSortedSubstates

 | Scan substates in a collection | Variable: `187 + IO access` |
| 

DrainSubstates

 | Drain substates in a collection | Variable: `273 * num_of_substates + 272 + IO access` |
| 

LockFee

 | Lock fee | Fixed: `500` |
| 

QueryFeeReserve

 | Query state of fee reserve | Fixed: `500`  |
| 

QueryActor

 | Query actor of this call frame | Fixed: `500`  |
| 

QueryTransactionHash

 | Query the transaction hash | Fixed: `500`  |
| 

GenerateRuid

 | Generate a RUID | Fixed: `500`  |
| 

EmitEvent

 | Emit an event | Variable: `500 + size(event) * 2` |
| 

EmitLog

 | Emit a log | Variable: `500 + size(log) * 2` |
| 

Panic

 | Panic and abort execution | Variable: `500 + size(message) * 2`    |
| Finalisation  
(finalisation cost unit) | 

CommitStateUpdates

 | Commit state updates | Variable, per substate:

-   Insert or update: `100,000 + size(substate) / 4`
-   Delete: `100,000`

 |
| 

CommitEvents

 | Commit events | Variable, per event: `5,000 + size(event) / 4` |
| 

CommitLogs

 | Commit logs | Variable, per log: `1,000 + size (log) / 4` |
| Storage  
(XRD) | 

IncreaseStateStorageSize

 | Increase the size of the state storage | Variable, per byte: `0.00009536743` |
| 

IncreaseArchiveStorageSize

 | Increase the size of the archive storage | Variable, per byte: `0.00009536743` |

IO access cost:

-   Read from database and found: `40,000 + size / 10`
-   Read from database and not found: `160,000`

## Costing Runtime APIs

The system exposes a list of API to query the current state of the fee reserve.

See [WASM API](https://github.com/radixdlt/radixdlt-scrypto/blob/6d35fe85de69d82b85700aaa9a68310a6163b72e/scrypto/src/engine/wasm_api.rs#L240-L258) and [Scrypto rustdoc](https://docs.rs/scrypto/latest/scrypto/runtime/struct.Runtime.html#method.get_execution_cost_unit_limit).

## Fee Distribution

| Cost | To Proposer | To Validator Set | To Burn | To Royalty Owners |
| --- | --- | --- | --- | --- |
| Execution | 25% | 25% | 50% | 0 |
| Finalisation | 25% | 25% | 50% | 0 |
| Storage | 25% | 25% | 50% | 0 |
| Royalty | 0 | 0 | 0 | 100% |
| Tip | 100% | 0 | 0 | 0 |