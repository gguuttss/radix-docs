---
title: "Cuttlefish (Live)"
---
# Cuttlefish (Live)

## Details

-   **Display Name:** Cuttlefish
-   **Logical Name:** `cuttlefish` and `cuttlefish-part2` (in two parts, back-to-back)
-   **Mainnet Enactment:**
    -   **Status:** Enacted
    -   **Epoch:** `105353`
    -   **Timestamp:** `2024-12-18T10:48:58Z`

## Main Changes

-   A new V2 transaction format supporting an [intent structure](../../reference/transactions/intent-structure.md) containing [subintents](../../reference/transactions/subintents.md), and the associated [pre-authorization flows](preauthorizations) to propose them in your dApp and have them reviewed and signed in the wallet.
    -   This is covered in depth in [this twitter space (starts at 4:30)](https://x.com/i/spaces/1vAxROdXqYVKl)
    -   This powers [Anthic Flash Liquidity](https://www.radixdlt.com/blog/technical-deep-dive-how-flash-liquidity-will-work)
-   Getters for balances on the native [Account](../../reference/radix-engine/native-blueprints/account.md) blueprint.
-   `CryptoUtils` for `blake256` hashing, `Secp256k1` and `Ed25519` validation.
-   Tweak to the consensus manager min rounds per epoch to make it much harder to miss 5 minute epochs.
-   Various improvements to the Radix Engine implementation.

## Release Notes

-   [Scrypto v1.3.0](../release-notes/scrypto/scrypto-v1-3-0.md)

## Libraries & SDKS

### Rust

-   [Engine rust crates](../../integrate/rust-libraries/rust-libraries-overview.md) (e.g. `scrypto`, `scrypto-test`, `radix-transactions`) - 1.3.0 ([crates.io](https://crates.io/crates/scrypto))

### Typescript

-   `@radixdlt/babylon-gateway-api-sdk` - 1.9.2 ([npm](https://www.npmjs.com/package/@radixdlt/babylon-gateway-api-sdk/v/1.9.2))
-   dApp Toolkit - 2.2.0 ([npm](https://github.com/radixdlt/radix-dapp-toolkit/releases/tag/v2.2.0))
-   Radix Engine Toolkit - *Typescript support for Transaction V2 will not be available at Cuttlefish launch*

### C#

-   Radix Engine Toolkit - v2.2.0 ([install guide](../../integrate/radix-engine-toolkit/radix-engine-toolkit-installation.md))

### Kotlin

-   Radix Engine Toolkit - v2.2.0 ([install guide](../../integrate/radix-engine-toolkit/radix-engine-toolkit-installation.md))

### Swift

-   Radix Engine Toolkit - v2.2.0 ([install guide](../../integrate/radix-engine-toolkit/radix-engine-toolkit-installation.md))

### Python

-   Radix Engine Toolkit - v2.2.0 ([install guide](../../integrate/radix-engine-toolkit/radix-engine-toolkit-installation.md))

### Go

-   Radix Engine Toolkit - v2.2.0 ([install guide](../../integrate/radix-engine-toolkit/radix-engine-toolkit-installation.md))