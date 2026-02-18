---
title: "Fungible Resource Manager"
---
# Fungible Resource Manager

This document offers a description of the design and implementation of the Fungible Resource Manager blueprint. Additionally, this document provides an API reference for all its methods, functions and events.

## Background

In Radix, a Resource is a native concept used to implement use-cases typically associated with “tokens” or “assets”. You can learn more about its high-level design at [Resources](resources).

A “Fungible Resource” is a kind of Resource operating on arbitrary quantities, which can be split and combined freely (i.e. its units do not have distinct identity nor individual metadata). [Detailed behaviors](resource-behaviors) of all units of a specific Fungible Resource is defined by its Fungible Resource Manager. This includes the rules for:

-   the Resource’s maximum divisibility,
    
-   minting and burning the Resource’s units,
    
-   freezing and recalling the Resource from Vaults,
    
-   tracking the Resource’s total supply.
    

Many functionalities of a Fungible Resource are implemented by Fungible Vault, Fungible Bucket and Fungible Proof, which are separate Native Blueprints (covered in detail by their respective documentation pages). The sections below focus on the `FungibleResourceManager` Blueprint itself.

## Optional Features

Not every Fungible Resource needs to satisfy the same set of use-cases. For this reason, the set of blueprint Features which can be optionally present on a `FungibleResourceManager` instance is quite broad:

-   `track_total_supply` - whether the total supply of the Resource should be tracked,
    
-   `vault_freeze` - whether a Vault holding the Resource can ever be frozen,
    
-   `vault_recall` - whether the Resource can ever be recalled from a Vault,
    
-   `mint` - whether more of the Resource (above initial supply) can ever be minted,
    
-   `burn` - whether the Resource can ever be burned.
    

## On-ledger State

The `FungibleResourceManager` blueprint defines two fields:

-   `divisibility`, holding an integer in the inclusive range `[0, 18]`, denoting a number of decimal places that the Resource can be split into. In other words: a precision of fixed-point fractional operations performed on the Resource’s amounts.
    
-   `total_supply` (only present if the `track_total_supply` Feature is enabled), holds an automatically-maintained amount of all units in circulation at any moment (i.e. minted so far and not burned yet).
    

## API Reference

### Functions

#### `create`

Defines a new fungible Resource (i.e. creates its Manager).

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:170px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" colwidth="170" style="width:170px;vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>create</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="170" style="width:170px;vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Function</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="170" style="width:170px;vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="170" style="width:170px;vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>owner_role</code>&nbsp;- <a href="https://github.com/radixdlt/radixdlt-scrypto/blob/01c421e4a5583f3c191f685fc322c1524600a911/radix-engine-interface/src/blueprints/resource/role_assignment.rs#L215" target="_self">OwnerRole</a>: The owner’s access rule (possibly <code>None</code>).</p><p class="p1"><code>track_total_supply</code>&nbsp;- <code>bool</code>: Whether to enable the supply-tracking <a href="validator-1#optional-features" target="_self">feature</a>.</p><p class="p1"><code>divisibility</code>&nbsp;- <code>u8</code>: The Resource unit’s divisibility (see its <a href="validator-1#onledger-state" target="_self">definition</a>).</p><p class="p1"><code>resource_roles</code>&nbsp;- <a href="https://github.com/radixdlt/radixdlt-scrypto/blob/01c421e4a5583f3c191f685fc322c1524600a911/radix-engine-interface/src/blueprints/resource/fungible/fungible_resource_manager.rs#L16" target="_self">FungibleResourceRoles</a>: The set of rules for all roles, including the optional ones (which cause&nbsp;their&nbsp;respective&nbsp;<a href="validator-1#optional-features" target="_self">features</a>&nbsp;to&nbsp;be&nbsp;enabled).</p><p class="p1"><code>metadata</code>&nbsp;- <code>ModuleConfig&lt;MetadataInit&gt;</code>: Configuration of metadata roles and the initial metadata values.</p><p class="p1"><code>address_reservation</code>&nbsp;- <code>Option&lt;GlobalAddressReservation&gt;</code>: An optional reservation of the global address.</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="170" style="width:170px;vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>ResourceAddress</code>: A&nbsp;de-facto&nbsp;identifier of the newly-created Resource; its Manager’s address.</p></td></tr></tbody></table>

#### `create_with_initial_supply`

Defines a new fungible Resource (i.e. creates its Manager) in the same way as [create](validator-1#create) does, and simultaneously mints the requested amount.

This variant is useful e.g. for creating a fixed supply of a non-mintable Resource.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:172px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" colwidth="172" style="width:172px;vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>create_with_initial_supply</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="172" style="width:172px;vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Function</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="172" style="width:172px;vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="172" style="width:172px;vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>owner_role</code>&nbsp;- <a href="https://github.com/radixdlt/radixdlt-scrypto/blob/01c421e4a5583f3c191f685fc322c1524600a911/radix-engine-interface/src/blueprints/resource/role_assignment.rs#L215" target="_self">OwnerRole</a>: The owner’s access rule (possibly <code>None</code>).</p><p class="p1"><code>track_total_supply</code>&nbsp;- <code>bool</code>: Whether to enable the supply-tracking <a href="validator-1#optional-features" target="_self">feature</a>.</p><p class="p1"><code>divisibility</code>&nbsp;- <code>u8</code>: The Resource unit’s divisibility (see its <a href="validator-1#onledger-state" target="_self">definition</a>).</p><p class="p1"><code>initial_supply</code> - <code>Decimal</code>: The amount to initially mint.</p><p class="p1"><code>resource_roles</code>&nbsp;- <a href="https://github.com/radixdlt/radixdlt-scrypto/blob/01c421e4a5583f3c191f685fc322c1524600a911/radix-engine-interface/src/blueprints/resource/fungible/fungible_resource_manager.rs#L16" target="_self">FungibleResourceRoles</a>: The set of rules for all roles, including the optional ones (which cause&nbsp;their&nbsp;respective&nbsp;<a href="validator-1#optional-features" target="_self">features</a>&nbsp;to&nbsp;be&nbsp;enabled).</p><p class="p1"><code>metadata</code>&nbsp;- <code>ModuleConfig&lt;MetadataInit&gt;</code>: Configuration of metadata roles and the initial metadata values.</p><p class="p1"><code>address_reservation</code>&nbsp;- <code>Option&lt;GlobalAddressReservation&gt;</code>: An optional reservation of the global address.</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="172" style="width:172px;vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>A tuple containing:</p><p><code>ResourceAddress</code>: A&nbsp;de-facto&nbsp;identifier of the newly-created Resource; its Manager’s address.</p><p><code>FungibleBucket</code>: A bucket with the entire initially-minted supply of the Resource.</p></td></tr></tbody></table>

### Methods

#### `mint`

Creates the requested amount of the Resource.

Note: the `mint` [feature](validator-1#optional-features) must be enabled on the Resource Manager.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:176px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" colwidth="176" style="width:176px;vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>mint</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="176" style="width:176px;vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="176" style="width:176px;vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public - requires the <code>minter</code> role.</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="176" style="width:176px;vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>amount</code> - <code>Decimal</code>: An amount to mint.</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="176" style="width:176px;vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>FungibleBucket</code>: A bucket with the newly-minted amount.</p></td></tr></tbody></table>

#### `burn`

Destroys the given bucket of the Resource.

Note: the `burn` [feature](validator-1#optional-features) must be enabled on the Resource Manager.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:192px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" colwidth="192" style="width:192px;vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>burn</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="192" style="width:192px;vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="192" style="width:192px;vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public - requires the <code>burner</code> role.</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="192" style="width:192px;vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>bucket</code> - <code>FungibleBucket</code>: A bucket with the amount to burn.</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="192" style="width:192px;vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Nothing</p></td></tr></tbody></table>

#### `package_burn`

Destroys the given bucket of the Resource, in the same way as `burn` does. This is an internal method needed to allow burning Resources from a Vault.

Note: the `burn` [feature](validator-1#optional-features) must be enabled on the Resource Manager.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:199px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" colwidth="199" style="width:199px;vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>package_burn</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="199" style="width:199px;vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="199" style="width:199px;vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Own package only.</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="199" style="width:199px;vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>bucket</code> - <code>FungibleBucket</code>: A bucket with the amount to burn.</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="199" style="width:199px;vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Nothing</p></td></tr></tbody></table>

#### `create_empty_vault`

Creates a new empty Vault tailored for storing the managed Resource and supporting the features configured on this Resource Manager.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:250px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>create_empty_vault</code></p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>None</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>Own</code>: The address of the created Vault.</p></td></tr></tbody></table>

#### `create_empty_bucket`

Creates a new empty Fungible Bucket tailored for holding the managed Resource.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:250px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>create_empty_bucket</code></p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>None</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>FungibleBucket</code>: The created Bucket.</p></td></tr></tbody></table>

#### `get_resource_type`

Queries the managed Resource’s type.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:165px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" colwidth="165" style="width:165px;vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>get_resource_type</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="165" style="width:165px;vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="165" style="width:165px;vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="165" style="width:165px;vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>None</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="165" style="width:165px;vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>ResourceType</code>: For a fungible Resource Manager this will always be <code>ResourceType::Fungible</code>, containing the Resource’s <a href="validator-1#onledger-state" target="_self">configured</a> <code>divisibility</code>.</p></td></tr></tbody></table>

#### `get_total_supply`

Queries the total amount of all units of this Resource currently in circulation.

Note: the `track_total_supply` [feature](validator-1#optional-features) must be enabled on the Resource Manager.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:185px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" colwidth="185" style="width:185px;vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>get_total_supply</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="185" style="width:185px;vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="185" style="width:185px;vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="185" style="width:185px;vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>None</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="185" style="width:185px;vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>Decimal</code>: The total supply amount.</p></td></tr></tbody></table>

#### `amount_for_withdrawal`

Converts the input amount to an actual withdrawal amount, taking the Resource’s [configured](validator-1#onledger-state) `divisibility` and the given rounding mode into account.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:213px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" colwidth="213" style="width:213px;vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>amount_for_withdrawal</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="213" style="width:213px;vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="213" style="width:213px;vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="213" style="width:213px;vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>request_amount</code> - <code>Decimal</code>: The approximate, requested amount.</p><p><code>withdraw_strategy</code> - <code>WithdrawStrategy</code>:</p><ul><li><p>either <code>Exact</code>, which returns the <code>request_amount</code> unchanged,</p></li><li><p>or one of the <a href="https://github.com/radixdlt/radixdlt-scrypto/blob/01c421e4a5583f3c191f685fc322c1524600a911/radix-engine-common/src/math/rounding_mode.rs#L16" target="_self">RoundingMode</a>s to be applied.</p></li></ul></td></tr><tr><td colspan="1" rowspan="1" colwidth="213" style="width:213px;vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>Decimal</code>: The actual, withdrawable amount.</p></td></tr></tbody></table>

#### `drop_empty_bucket`

Drops the given empty Fungible Bucket.

Note: passing a non-empty Bucket will result in an error.

<table width="100%" class="editor360-table" borderstyle="solid"><colgroup style="display:none;"><col style="width:196px;"><col style="width:250px;"></colgroup><tbody><tr><td colspan="1" rowspan="1" colwidth="196" style="width:196px;vertical-align:middle;text-align:left;"><p><strong>Name</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>drop_empty_bucket</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="196" style="width:196px;vertical-align:middle;text-align:left;"><p><strong>Type</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="196" style="width:196px;vertical-align:middle;text-align:left;"><p><strong>Callable By</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Public</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="196" style="width:196px;vertical-align:middle;text-align:left;"><p><strong>Arguments</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>bucket</code> - <code>FungibleBucket</code>: The bucket to drop.</p></td></tr><tr><td colspan="1" rowspan="1" colwidth="196" style="width:196px;vertical-align:middle;text-align:left;"><p><strong>Returns</strong></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Nothing</p></td></tr></tbody></table>

### Events

A `FungibleResourceManager` component instance can be a source of the following events:

#### `VaultCreationEvent`

```rust
{
  // The ID of the created Vault.
  vault_id: NodeId
}
```

Emitted when a new Vault for the managed Resource is created (i.e. on `create_empty_vault` API call).

#### `MintFungibleResourceEvent`

```rust
{
  // The minted amount.
  amount: Decimal,
}
```

Emitted when some amount of the managed Resource is minted (i.e. on the explicit `mint` API call, but also on `create_with_initial_supply`).

#### `BurnFungibleResourceEvent`

```rust
{
  // The burned amount.
  amount: Decimal
}
```

Emitted when some amount of the managed Resource is burned (i.e. on the explicit `burn/package_burn` API calls, but also internally when burning the transaction fees).