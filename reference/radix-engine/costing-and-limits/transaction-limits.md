---
title: "Transaction Limits"
---
# Transaction Limits

## SBOR Limits

In Radix Engine, almost all payloads are encoded [Scrypto SBOR](../../sbor-serialization/scrypto-sbor/README.md) or [Manifest SBOR](../../sbor-serialization/manifest-sbor/README.md), including but not limited to:

-   Transaction payload
-   Component state
-   Function invocation input and output
-   System APIs

When the engine validates or decodes these payloads, it applies the following additional limits:

| Limit | Value | Description |
| --- | --- | --- |
| `BLUEPRINT_PAYLOAD_MAX_DEPTH` | 48 | The max depth of blueprint payload, such as component state, events and function input/output. |
| `KEY_VALUE_STORE_ENTRY_MAX_DEPTH` | 48 | The max depth of KeyValueStore entries, both keys and values. |

## Transaction Limits

| Limit | Value | Description |
| --- | --- | --- |
| `MAX_NUMBER_OF_INTENT_SIGNATURES` | 16 | The maximum number of signatures that can be used to sign a transaction intent. This does not include notary signature. |
| `MAX_NUMBER_OF_BLOBS` | 16 | The maximum number of blobs a transaction can include. |
| `MIN_TIP_PERCENTAGE` | 0% | The minimum tip percentage. |
| `MAX_TIP_PERCENTAGE` | 65,535% | The maximum tip percentage. |
| `MAX_EPOCH_RANGE` | 8640 | The maximum allowed epoch range in the transaction header. This is roughly about 30 days assuming a 5-minute epoch time. |
| `MAX_TRANSACTION_SIZE` | 1 MiB | The maximum transaction payload size. |

## System Limits

| Limit | Value | Description |
| --- | --- | --- |
| `EXECUTION_COST_UNIT_LIMIT` | 100,000,000 | The maximum number of execution cost units that a transaction can use. |
| `FINALISATION_COST_UNIT_LIMIT` | 50,000,000 | The maximum number of finalisation cost units that a transaction can use. |
| `PREVIEW_CREDIT_IN_XRD` | 1,000,000 | The amount of XRD that is used as transaction fee when previewing a manifest intent. |
| `MAX_CALL_DEPTH` | 8 | The max depth of the call frames.  
Depth increases by 1 when a function or method is invoked and decreases by 1 after the invocation is finished. |
| `MAX_HEAP_SUBSTATE_TOTAL_SIZE` | 64 MiB | The maximum total size of substates in the Heap.
-   Both substate keys and values are accounted.
-   Heap is the storage that keeps all the substates of nodes that are neither globalised nor owned by a global object.

 |
| `MAX_TRACK_SUBSTATE_TOTAL_SIZE` | 64 MiB | The maximum total size of substates in the Heap.

-   Both substate keys and values are accounted.
-   Track is the storage that keeps all the substates of "loaded" globalised nodes and their children.

 |
| `MAX_SUBSTATE_KEY_SIZE` | 2 KiB | The maximum size of a substate key. |
| `MAX_SUBSTATE_VALUE_SIZE` | 2 MiB | The maximum size of a substate value. |
| `MAX_INVOKE_PAYLOAD` | 1 MiB | The maximum size of invocation payload, encoding of the call arguments. |
| `MAX_EVENT_SIZE` | 32 KiB | The maximum size of a single event. |
| `MAX_NUMBER_OF_EVENTS` | 256 | The maximum number of events that a transaction can produce. |
| `MAX_LOG_SIZE` | 32 KiB | The maximum size of a single log. |
| `MAX_NUMBER_OF_LOGS<` | 256 | The maximum number of logs that a transaction can produce. |
| `MAX_PACK_MESSAGE_SIZE` | 32 KiB | The maximum size of a panic message. |

## WASM Limits

| Limit | Value | Description |
| --- | --- | --- |
| `MAX_MEMORY_SIZE_IN_PAGES` | 8 | The maximum depth of an access rule. |
| `MAX_INITIAL_TABLE_SIZE` | 1024 | The maximum initial table size. |
| `MAX_NUMBER_OF_BR_TABLE_TARGETS` | 256 | The maximum of BR table targets. |
| `MAX_NUMBER_OF_GLOBALS` | 512 | The maximum number of global variables. |
| `MAX_NUMBER_OF_FUNCTIONS` | 8192  
 | The maximum number of functions. |
| `MAX_NUMBER_OF_FUNCTION_PARAMS` | 32 | The maximum of parameters in a single function. |
| `MAX_NUMBER_OF_FUNCTION_LOCALS` | 256 | The maximum of locals in a single function. |
| `MAX_NUMBER_OF_BUFFERS` | 32 | The max number of buffers that a Scrypto VM can allocate.  
Buffers are used for data exchange between system and Scrypto blueprints. |

## Metadata Limits

| Limit | Value | Description |
| --- | --- | --- |
| `MAX_METADATA_KEY_STRING_LEN` | 100 | The maximum length of a metadata key (in characters). |
| `MAX_METADATA_VALUE_SBOR_LEN` | 4 KiB | The maximum size of a metadata value (measured in bytes of the SBOR encoding). |
| `MAX_URL_LENGTH` | 1024 | The maximum length of a URL. |
| `MAX_ORIGIN_LENGTH` | 1024 | The maximum length of an Origin. |
| `MAX_ACCESS_RULE_DEPTH` | 8 | The maximum depth of an access rule. |
| `MAX_ACCESS_RULE_TOTAL_NODES` | 64 | The maximum total number of nodes in an access rule. |

## Access Rule Limits

| Limit | Value | Description |
| --- | --- | --- |
| `MAX_ACCESS_RULE_DEPTH` | 8 | The maximum depth of an access rule. |
| `MAX_ACCESS_RULE_TOTAL_NODES` | 64 | The maximum total number of nodes in an access rule. |

## Royalty Limits

| Limit | Value | Description |
| --- | --- | --- |
| `MAX_PER_FUNCTION_ROYALTY_AMOUNT_IN_XRD` | 166.666666666666666666 | The maximum royalty amount (converted into XRD) that can be set on a function.  
This is roughly 10 USD. |

## Blueprint Package Limits

| Limit | Value | Description |
| --- | --- | --- |
| `MAX_FEATURE_NAME_LEN` | 100 | The maximum length of a feature name.  
**Note**: The "features" is a capability exposed to native blueprints only. |
| `MAX_EVENT_NAME_LEN` | 100 | The maximum length of an event name. |
| `MAX_REGISTERED_TYPE_NAME_LEN   ` | 100 | The maximum length of the name of a registered type. |
| `MAX_BLUEPRINT_NAME_LEN   ` | 100 | The maximum length of a blueprint name. |
| `MAX_FUNCTION_NAME_LEN   ` | 256 | The maximum length of a function name. |
| `MAX_NUMBER_OF_BLUEPRINT_FIELDS   ` | 256 | The maximum number of fields defined in a blueprint.  
**Note**: Scrypto blueprints have only one field as of now.  
 |