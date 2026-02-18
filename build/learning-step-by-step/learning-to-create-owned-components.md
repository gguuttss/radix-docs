---
title: "Create Owned Components"
---
# Create Owned Components

In the last section we looked at a candy store package made up of several blueprints. This section will show how to do the same thing in a different way. We will still have a candy store component containing a gumball machine component. The difference this time, will be the gumball machine will be **owned** by the candy store.

There are two broad ways to modularise your components, each with distinct advantages.

1.  Global components: Like all the components from the previous sections, these are created at the global level and are accessible to all other components on the ledger.
2.  Owned components: Internal to other components, they are only accessible to their parent components.

The scrypto package referenced in this section can be found in our [official examples here](https://github.com/radixdlt/official-examples/tree/main/step-by-step/16-candy-store-owned-modules).

## Global and Owned Components

All components are initially local. In this state they are not addressable by or accessible to others. To change this we globalize them. This is done after instantiation by first calling `prepare_to_globalize` (setting the component access rules and reserving an address) on the new component, then calling the `globalize` method like so:

```
    .instantiate()
    .prepare_to_globalize(OwnerRole::None)
    .globalize();
```

Without these steps, to use a component it must be internal to another. This is done by adding the component in a parent component's struct, with the wrapping `Owned` type, e.g.

```
    struct CandyStore {
        gumball_machine: Owned<GumballMachine>,
    }
```

The owned component's methods, can then be called by it's parent, but no other components. e.g.

```
    self.gumball_machine.buy_gumball(payment);
```

Let's compare how this looks in our example packages.

Package with Owned GumballMachine

Package with Global GumballMachine

![image.png](https://cdn.document360.io/50e78792-5410-4ac9-aa43-4612b4d33953/Images/Documentation/image%2813%29.png)

![image.png](https://cdn.document360.io/50e78792-5410-4ac9-aa43-4612b4d33953/Images/Documentation/image%2814%29.png)

### Owned vs Global `GumballMachine`

The global `GumballMachine` is the same as from previous examples. The owned version is where we see several changes.

#### Restricted methods

Owned

Global

-   No method restrictions. This is now handled by the parent component.

```
enable_method_auth! {
    methods {
        buy_gumball => PUBLIC;
        get_status => PUBLIC;
        set_price => restrict_to: [OWNER];
        withdraw_earnings => restrict_to: [OWNER];
        refill_gumball_machine => restrict_to: [OWNER];
}}
```

#### Instantiation function start

Owned

Global

-   The parent component address is passed in.
-   Only the new component is returned as there's no owner badge.

```
pub fn instantiate_gumball_machine(
   price: Decimal,
   parent_component_address:
    ComponentAddress,
) -> Owned<GumballMachine> {
```

-   A component address reserved.
-   Both the component and owner badge are returned.

```
pub fn instantiate_gumball_machine(
  price: Decimal
) ->
   (Global<GumballMachine>, Bucket) {
      // reserve an address for the component
      let (
        address_reservation,
        component_address,
      ) =
         Runtime::allocate_component_address(
            GumballMachine::blueprint_id()
         );
```

#### Owner badge

Owned

Global

-   No owner badge. The parent component is the owner.

```
let owner_badge = ...
```

#### Gumball mint roles

Owned

Global

```
    .mint_roles(mint_roles! {
        minter => rule!(require(
              global_caller(
                  parent_component_address
            )));
        minter_updater => rule!(deny_all);
    })
```

```
    .mint_roles(mint_roles! {
        minter => rule!(require(
            global_caller(
                component_address
            )));
        minter_updater => rule!(deny_all);
    })
```

#### Instantiation function end

Owned

Global

```
    .instantiate()
```

```
    .instantiate()
    .prepare_to_globalize(OwnerRole::Fixed(rule!(require(
        owner_badge.resource_address()
     ))))
    .with_address(address_reservation)
    .globalize();
```

The owned version is simpler, as we're able to remove much of the access and control code. The possible downside is the owned component no longer has a global address so cannot be accessed by components other than it's owner, such as those in other packages. For some purposes this is ideal though.

### Owner vs Non-owner `CandyStore`

The `CandyStore` is globalized in both (this and the previous sections) version of our package. The code for its blueprint is simpler here, where it owns the `GumballMachine`. In this version there's no need for a `GumballMachine` owner badge stored in this component. The only methods accessible on either blueprint are those of the `CandyStore`, so we restricted access to necessary methods on the `CandyStore` and we don't need add restrictions to the `GumballMachine`. No method restrictions in the `GumballMachine` means no needs to hold the `GumballMachine` owner badge to pass proof of ownership to the `GumballMachine` to call it's restricted methods. This is done with the `authorize_with_amount` method for the previous global `GumballMachine`, which we don't have to use at all in this version.

#### Component state

Owner (owned GumballMachine)

Non-owner (global GumballMachine)

```
struct CandyStore {
    gumball_machine: Owned<GumballMachine>,
}
```

```
struct CandyStore {
    gumball_machine: Global<GumballMachine>,
    gumball_machine_owner_badges: Vault,
}
```

#### Address reservation

Owner (owned GumballMachine)

Non-owner (global GumballMachine)

```
let (address_reservation, component_address) =
    Runtime::allocate_component_address(
        CandyStore::blueprint_id()
    );
```

-   None. The `GumballMachine` address and owner badge are used to restrict access to component methods and token behaviours.
    

#### Gumball machine instantiation

Owner (owned GumballMachine)

Non-owner (global GumballMachine)

```
let gumball_machine =
    GumballMachine::instantiate_gumball_machine(
        gumball_price,
        component_address,
     );
```

```
let (
       gumball_machine,
       gumball_machine_owner_badge,
     ) =
    GumballMachine::instantiate_gumball_machine(
        gumball_price
    );
```

#### Globalizing

Owner (owned GumballMachine)

Non-owner (global GumballMachine)

```
  .instantiate()
  .prepare_to_globalize(
    OwnerRole::Fixed(rule!(require(
      owner_badge.resource_address()
   ))))
  .with_address(address_reservation)
  .globalize();
```

```
    .instantiate()
    .prepare_to_globalize(
      OwnerRole::Fixed(rule!(require(
        owner_badge.resource_address()
     ))))
    .globalize();
```

#### Calling restricted GumballMachine methods

Owner (owned GumballMachine)

Non-owner (global GumballMachine)

```
pub fn set_gumball_price(
  &mut self, new_price: Decimal
) {
  self.gumball_machine.set_price(
    new_price
  );
}
```

-   To call a method on the GumballMachine we need to pass a proof that we have it's owner badge.

```
pub fn set_gumball_price(
      &mut self, new_price: Decimal
) {
  self.gumball_machine_owner_badges
    .as_fungible()
    .authorize_with_amount(
      1,
      || self.gumball_machine.set_price(
        new_price
    ));
}
```

Owned components make for simpler code that is easier to read and maintain. But you will have to decide if removing global accessibility of some component works for your applications.

## Using the Candy Store

Instructions on how to setup and use the multi-blueprint yourself can be found in the [Official Example GitHub repository](https://github.com/radixdlt/official-examples/tree/main/step-by-step/16-candy-store-owned-modules#using-the-candy-store)