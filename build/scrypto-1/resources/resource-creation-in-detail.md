---
title: "Resource Creation in Detail"
---
# Resource Creation in Detail

## The `ResourceBuilder`

The `ResourceBuilder` is your utility for creating new resources. You have a number of resource creation methods to make things easy.

You can start using it like this `ResourceBuilder::new_` then complete the building process using either `create_with_no_initial_supply()` or `mint_initial_supply(..)`.

> What’s returned
> 
> It’s important to note that `create_with_no_initial_supply()` will return a `ResourceManager` where as `mint_initial_supply(..)` will return a bucket with the created supply of resources.

### New Resource Methods

<table width="100%" class="editor360-table" borderstyle="Solid"><colgroup style="display:none;"><col style="width:250px;"><col style="width:250px;"><col style="width:250px;"></colgroup><tbody><tr><th colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></th><th colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Arguments</p></th><th colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Returns</p></th></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>new_fungible()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>owner_role</code> : <code>OwnerRole</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>InProgressResourceBuilder&lt;FungibleResourceType&gt;</code></p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>new_string_non_fungible()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><ul><li><p><code>owner_role</code> : <code>OwnerRole</code></p></li><li><p><code>&lt;NonFungibleData&gt;</code></p></li></ul></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>InProgressResourceBuilder&lt;NonFungibleResourceType&lt;StringNonFungibleLocalId, D&gt;&gt;</code></p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>new_integer_non_fungible()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><ul><li><p><code>owner_role</code> : <code>OwnerRole</code></p></li><li><p><code>&lt;NonFungibleData&gt;</code></p></li></ul></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>InProgressResourceBuilder&lt;NonFungibleResourceType&lt;IntegerNonFungibleLocalId, D&gt;&gt;</code></p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>new_bytes_non_fungible()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><ul><li><p><code>owner_role</code> : <code>OwnerRole</code></p></li><li><p><code>&lt;NonFungibleData&gt;</code></p></li></ul></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>InProgressResourceBuilder&lt;NonFungibleResourceType&lt;BytesNonFungibleLocalId, D&gt;&gt;</code></p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>new_ruid_non_fungible()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><ul><li><p><code>owner_role</code> : <code>OwnerRole</code></p></li><li><p><code>&lt;NonFungibleData&gt;</code></p></li></ul></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>InProgressResourceBuilder&lt;NonFungibleResourceType&lt;RUIDNonFungibleLocalId, D&gt;&gt;</code></p></td></tr></tbody></table>

Read the [Resource Behaviors](resource-behaviors) article for further customization options on the builder, including:

-   Setting the `OwnerRole`
    
-   Setting custom behaviours
    
-   Setting divisibility for fungible resources
    
-   Setting metadata
    

Here is an example creating a very simple fungible resource:

```rust
let my_token: FungibleBucket = ResourceBuilder::new_fungible(OwnerRole::None)
    .metadata(metadata!(
        init {
            "name" => "My Token", locked;
            "symbol" => "TKN", locked;
        }
    ))
    .divisibility(DIVISIBILITY_NONE) // No decimals allowed
    .mint_initial_supply(100);
```

The Non-Fungible variants also require a struct of `NonFungibleData` as you can see below:

```rust
// The top-level struct must derive NonFungibleData.
// Note that marking top-level fields as `#[mutable]` means that the data under
// that field can be updated by the `resource_manager.update_non_fungible_data(...)` method.
//
// All types referenced directly/indirectly also need to derive ScryptoSbor.
// To work with the Manifest Builder, we recommend all types also derive ManifestSbor.
#[derive(ScryptoSbor, ManifestSbor, NonFungibleData)]
struct GameData {
  team_one: String,
  team_two: String,
  section: String,
  seat_number: u16,
  #[mutable]
  promo: Option<String>,
}

#[blueprint]
mod nftblueprint {
    struct NftBlueprint {
    }

    impl NftBlueprint {
        pub fn create_game_nfts() {
            ResourceBuilder::new_integer_non_fungible::<GameData>(OwnerRole::None)
                .metadata(metadata! {
                     init {
                       "name" => "Mavs vs Lakers - 12/25/2023", locked;
                       "description" => "Tickets to the 2023 season of the Dallas Mavericks", locked;
                     }
                 )
                .create_with_no_initial_supply();
        }
    }
}
```

## The `ResourceManager`

When a resource is created on the Radix network, a “Resource Manager” with a unique address will be associated with it. This manager contains the data of this resource such as its type (fungible or non-fungible), its metadata and its supply among other things. It also allows people with the right authority to do actions on the resource like minting more tokens and updating its metadata. On this page, we will show you how to use the `ResourceManager` type that Scrypto offers and the various methods you can call on it.

### Methods available on ResourceManager

The `ResourceManager` offers many methods that we listed in the following tables.

#### Fetching resource information

<table width="100%" class="editor360-table" borderstyle="Solid"><colgroup style="display:none;"><col style="width:250px;"><col style="width:250px;"></colgroup><tbody><tr><th colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></th><th colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Description</p></th></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>get_metadata(key)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Returns the metadata associated with the specified key.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>resource_type()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Returns a ResourceType that is either non-fungible or fungible.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>total_supply()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Returns the total supply of the resource.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>non_fungible_exists(NonFungibleLocalId)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Returns whether a token with the specified non-fungible id was minted.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>get_non_fungible_data(NonFungibleLocalId)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Returns the data associated with a particular non-fungible token of this resource.</p></td></tr></tbody></table>

#### Applying actions

Provided that the correct authority is presented (more information about this [here](resource-behaviors)), you can apply the following actions to a resource.

<table width="100%" class="editor360-table" borderstyle="Solid"><colgroup style="display:none;"><col style="width:250px;"><col style="width:250px;"></colgroup><tbody><tr><th colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></th><th colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Description</p></th></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>set_metadata(key, value)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Updates a metadata key associated with this resource.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>mint(amount)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Mints an amount of fungible tokens.</p><p><strong>Note</strong>: The resource must be of the fungible type.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>mint_non_fungible(&amp;id, data)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Mints a non-fungible token with the specified id and data.</p><p><strong>Note</strong>: The resource must be of the non-fungible type</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>update_non_fungible_data(&amp;id, field_name, new_data)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Updates a single field of the non-fungible data associated with a minted non-fungible token of the specified ID.</p></td></tr></tbody></table>

#### Updating resource flags

More information on access rules and resource flags [here](resource-behaviors).

<table width="100%" class="editor360-table" borderstyle="Solid"><colgroup style="display:none;"><col style="width:250px;"><col style="width:250px;"></colgroup><tbody><tr><th colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Method</p></th><th colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Description</p></th></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>set_mintable(access_rule)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Sets the access rule for being able to update the <code>mintable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>lock_mintable()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Locks the <code>mintable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>set_burnable(access_rule)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Sets the access rule for being able to update the <code>burnable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>lock_burnable()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Locks the <code>burnable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>set_withdrawable(access_rule)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Sets the access rule for being able to update the <code>withdrawable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>lock_withdrawable()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Locks the <code>withdrawable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>set_depositable(access_rule)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Sets the access rule for being able to update the <code>depositable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>lock_depositable()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Locks the <code>depositable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>set_recallable(access_rule)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Sets the access rule for being able to update the <code>recallable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>lock_recallable()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Locks the <code>recallable</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>set_updatable_metadata(access_rule)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Sets the access rule for being able to update the <code>updateable_metadata</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>lock_updatable_metadata()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Locks the <code>updateable_metadata</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>set_updatable_non_fungible_data(access_rule)</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Sets the access rule for being able to update the <code>updateable_non_fungible_data</code> flag.</p></td></tr><tr><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p><code>lock_updatable_non_fungible_data()</code></p></td><td colspan="1" rowspan="1" style="vertical-align:middle;text-align:left;"><p>Locks the <code>updateable_non_fungible_data</code> flag.</p></td></tr></tbody></table>

The owner role can be updated with `set_owner_role` and `lock_owner_role`.

## Related Rust docs

-   [ResourceBuilder](https://docs.rs/scrypto/latest/scrypto/resource/resource_builder/struct.ResourceBuilder.html)
    
-   [ResourceManager](https://docs.rs/scrypto/latest/scrypto/resource/resource_manager/struct.ResourceManager.html)
    
-   [ResourceManagerStub](https://docs.rs/scrypto/latest/scrypto/resource/resource_manager/struct.ResourceManagerStub.html)