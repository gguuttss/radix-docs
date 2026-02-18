---
title: "Network APIs"
---
# Network APIs

## Main APIs

Radix currently offers a couple of few main APIs, targeted to different use cases.

### Core API

**Key Links:** [**Full Core API Documentation**](https://radix-babylon-core-api.redoc.ly/) **|** [**Core API Providers**](core-api-providers.md) **|** [**Typescript Core API SDK**](https://www.npmjs.com/package/@radixdlt/babylon-core-api-sdk)

The Core API provides low-level and high-level abstractions, and includes:

-   The long-term support “LTS” sub-api, designed for financial integrators.
    
-   A usefully-abstracted but not comprehensive view of the current ledger state. This includes account balances, and other key native components.
    
-   Transaction preview, submission + status flow
    
-   A committed transaction stream, at varying abstraction levels
    

The Core API is exposed by [Radix nodes](run-infrastructure). It is predominantly intended as a private API, but there [are some RPC providers offering it](core-api-providers.md) for public integrations.

### Gateway API

**Key Links:** [**Full Gateway API Documentation**](https://radix-babylon-gateway-api.redoc.ly/) **|** [**Gateway API Providers**](gateway-api-providers.md) **|** [**Typescript Gateway API SDK**](https://www.npmjs.com/package/@radixdlt/babylon-gateway-api-sdk)

The Gateway API provides low-level and high-level abstractions, but is primarily intended for use by dApp website frontends and general network clients like dashboards. It can be used for:

-   Reading the state of accounts or other components
    
-   Transaction preview, submission + status flow; including managing of resubmissions on behalf of users
    
-   A filterable committed transaction history
    
-   Queries of historic ledger state
    

The Gateway API is exposed by the [Network Gateway](run-infrastructure), and provided by [Gateway API Providers](gateway-api-providers.md).

### Engine State API

**Key Links:** [**Full Engine State API Documentation**](https://radix-babylon-engine-state-api.redoc.ly/)

The Engine State API allows for accessing the complete current state of the Engine ledger, at the abstraction of the engine.

-   Compared to the Core API, it is fully comprehensive, but possibly harder to use, but has fewer, more general endpoints.
    
-   It can be useful for some dApp developers looking to read current application state without running a Gateway, and at a cleaner abstraction level.
    
-   It is useful for integrators to explore how the engine thinks about state.
    

The Engine State API is exposed by [Radix nodes](run-infrastructure). The node has to be explicitly configured to enable this API. Details on how to configure the node [can be found in the v1.1.3.1 release notes](https://github.com/radixdlt/babylon-node/releases/tag/v1.1.3.1).

* * *

## Community APIs

If the Core and Gateway APIs don’t currently meet your needs, members of the community have built their own APIs, which may have just the endpoint you’re looking for.

> These are unvetted
> 
> Inclusion in this list does *not* imply endorsement or make any claims of correctness or stability. Please look at the individual service for more details, and judge for yourself if it fits your needs.
> 
> If you are are responsible for providing an API service and would like it to be included here, please get in touch on Discord or at hello@radixdlt.com.

### RadixAPI

**Key Links:** [**Website**](https://radixapi.net/) **|** [**Telegram Channel**](https://t.me/radixapi)

[RadixAPI](https://radixapi.net/) provides additional [endpoints and functionality](https://docs.radixapi.net/) to the official Gateway API to make it easier to obtain specific data. These include:

-   Creating and verifying [ROLA](../../build/build-dapps/dapp-application-stack/rola-radix-off-ledger-auth.md) challenges
    
-   Finding the owners of a fungible or non fungible resource
    
-   Finding the owners of validator stake and pool unit tokens
    
-   Receiving all transaction data via WebSocket
    
-   …and more
    

* * *

## Other APIs

### System API

**Key Links:** [**Full System API Documentation**](https://radix-babylon-system-api.redoc.ly/)

Radix Nodes also expose the System API, which can be used for debugging the node. Its [documentation is available on ReDocly here](https://radix-babylon-system-api.redoc.ly/).