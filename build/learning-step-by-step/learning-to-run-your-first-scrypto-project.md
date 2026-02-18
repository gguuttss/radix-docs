---
title: "Run Your First Scrypto Project"
---
# Run Your First Scrypto Project

"Hello, World!" examples usually provide the simplest possible piece of code to understand the basics of a new language. However, Scrypto isn't just a typical language – it is specialized for the management of assets on a decentralized network. So rather than just printing `Hello, World!` to a console, we'll hand out a `HelloToken`!

In this learning journey section, we'll walk you through running and interacting with Scrypto within the Radix Engine Simulator. By the end, you'll have locally deployed and used your first Scrypto "smart contract".

This section focuses on getting existing code working on your machine. In the [Explain Your First Scrypto Project](/learning-to-explain-your-first-scrypto-project) article, we will delve deeper into what the code is doing.

Before we begin

Make sure you have the [Scrypto toolchain installed](../setting-up-for-scrypto-development/getting-rust-scrypto.md). If you do, let's get started!

## Developing on Radix

A simple software development workflow might involve writing code, conducting local testing, deploying to a remote test environment for further testing, and finally deploying to the production environment. When it comes to creating smart contracts on Radix, we follow a similar process.

We use Scrypto to write the code and the Radix Engine Simulator (`resim`) for local testing. Once you're satisfied with your code, you can deploy it to our test network (Stokenet) and then to the main Radix Network (Mainnet).

Our development steps are:

1.  Create a package.
2.  Define code with the business logic
3.  Deploy the code package - using resim for local testing, Stokenet for testing, or Mainnet for production.
4.  Instantiate a component from the package.

Let's start these steps.

## Creating a New Blueprint Package

Development starts by creating a package. We'll use the `scrypto` command line tool for this.

-   Open a new terminal in the directory where you'd like to create your new project and run the command below.
    
```bash
    scrypto new-package tutorial
    ```
    

This scaffolds a simple package containing a `Hello` blueprint.

Blueprints and Packages

Radix splits the concept of "smart contracts" into two parts:

-   A **blueprint** is the code that defines the behavior of the application and what state it will manage. Blueprints are instantiated into **components**, which then get their own address and own state. In object-oriented programming terms, you can think of it like a blueprint being a class definition and a component being an object which is an instance of that class.
-   Blueprints are always part of a **package** when deployed. Multiple blueprints may be grouped together in a single package.

-   Open the new package directory in an IDE/code editor with Rust support.

Choosing a code editor

VSCode is where we've focused most of our efforts on tooling so far.  
Find the [instructions on how to setup VSCode here](../setting-up-for-scrypto-development/choosing-an-ide.md).

-   Then open up `src/lib.rs` to see the source that we’re going to compile and deploy to the simulator.
    
```bash
    code tutorial/src/lib.rs
    ```
    

The `Hello` blueprint has one function which creates a new component with a supply of tokens (`instantiate_hello`), and one method to get one of those tokens from the component (`free_token`). A detailed explanation of this blueprint is in the [next learning journey section](learning-to-explain-your-first-scrypto-project.md).

So far we've completed steps;

1.  Create a package, and
2.  Define blueprints with the business logic.

Next we deploy the package in the Radix Engine Simulator.

## Deploying the package locally via Radix Engine Simulator `resim`

On distributed ledgers like Radix, each action on the network incurs transaction fees. [Transaction fees](../../reference/radix-engine/costing-and-limits/transaction-costing.md) reflect the load each transaction puts on the network and are used to compensate the [network validators](../../reference/radix-engine/native-blueprints/validator.md).

To deploy our package, we will therefore need an account with enough XRD to cover the transaction fees. We can create both in `resim`.

### Creating an Account

`resim` has simple commands to create and use accounts in the simulator environment.

-   Most `resim` commands use an existing account, so before we do anything else, lets create one.
    
```bash
    resim new-account
    ```
    

You should get a success message. At the bottom of the output you should see the created account component address, public/private key, and the `NonFungibleGlobalId` of your owner badge. e.g.:

```plainText
A new account has been created!
Account component address: account_sim1c956qr3kxlgypxwst89j9yf24tjc7zxd4up38x37zr6q4jxdx9rhma
Public key: 03b9813c2244f864d3f886a8d8716aff5d234fab83a1c6f39d60999ddb01a15f98
Private key: 2ce29b819e212e999c8bd3ab05f529b7868a4667dfe6db03b257ab0560525635
Owner badge: resource_sim1nfzf2h73frult99zd060vfcml5kncq3mxpthusm9lkglvhsr0guahy:#1#
Set up as default account since you had none, you can change it using `set-default-account`.
```

Warning

Do not attempt to use this private key on the main network! It is seeded from a predictable value and stored in plaintext.

-   If you do not see the line about setting your default account, then you already created an account previously. Either reset your entire simulator with the `resim reset` command and try again, or set this new account as your default account with the following command:
    
```bash
    resim set-default-account <ACCOUNT_ADDRESS> <PRIVATE_KEY> <OWNER_BADGE>
    ```
    

### Displaying the Account

-   You can look at the resources in your new account by running:
    
```bash
    resim show <ACCOUNT_ADDRESS>
    ```
    

`resim show`

The `show` command can be used with any account component, component or resource address.

You will get something like this:

```plainText
Component Address: account_sim1c956qr3kxlgypxwst89j9yf24tjc7zxd4up38x37zr6q4jxdx9rhma
Blueprint ID: { package_address: package_sim1pkgxxxxxxxxxaccntxxxxxxxxxx000929625493xxxxxxxxxrn8jm6, blueprint_name: "Account" }
Owned Fungible Resources: 1
└─ resource_sim1tknxxxxxxxxxradxrdxxxxxxxxx009923554798xxxxxxxxxakj8n3: 10000 Radix (XRD)
Owned Non-fungibles Resources: 1
└─ resource_sim1nfzf2h73frult99zd060vfcml5kncq3mxpthusm9lkglvhsr0guahy: 1 Owner Badge
   └─ #1#
Metadata: 0
```

Note the `Owned Fungible Resources:` section, which indicates that the account already holds 1000 XRD, which will be more than enough to deploy our first package.

### Deploying the Package

-   To publish/deploy our package, we need to make sure our terminal has navigated to the project root directory containing the `Cargo.toml` file:
    
```bash
    cd tutorial
    ```
    
-   Then we use the radix engine simulator to publish/deploy our package locally
    
```bash
    resim publish .
    ```
    

Once this finishes you should see the published package’s address at the end of the output. e.g.

```plainText
Success! New Package: package_sim1pk3cmat8st4ja2ms8mjqy2e9ptk8y6cx40v4qnfrkgnxcp2krkpr92
```

-   **Store the address** for the last of our 4 development steps, instantiating a component from our package

### Component Instantiation

-   Now our package is deployed "on ledger" in the simulator, we're going to create a component by calling the `instantiate_hello` function on the `Hello` blueprint.
    
```bash
    resim call-function <PACKAGE_ADDRESS> Hello instantiate_hello
    ```
    

**You can find an example and explanation of the function call output by clicking here**

The `instantiate_hello` output should look something like this:

```rust
Transaction Status: COMMITTED SUCCESS
Transaction Cost: 0.65177966419 XRD
├─ Network execution: 0.30327475 XRD, 6065495 execution cost units
├─ Network finalization: 0.1260127 XRD, 2520254 finalization cost units
├─ Tip: 0 XRD
├─ Network Storage: 0.22249221419 XRD
└─ Royalties: 0 XRD
Logs: 0
Events: 7
├─ Emitter: Method { node: internal_vault_sim1tz9uaalv8g3ahmwep2trlyj2m3zn7rstm9pwessa3k56me2fcduq2u, module_id: Main }
   Event: LockFeeEvent {
     amount: Decimal("5000"),
   }
├─ Emitter: Method { node: resource_sim1t4h3kupr5l95w6ufpuysl0afun0gfzzw7ltmk7y68ks5ekqh4cpx9w, module_id: Main }
   Event: MintFungibleResourceEvent {
     amount: Decimal("1000"),
   }
├─ Emitter: Method { node: resource_sim1t4h3kupr5l95w6ufpuysl0afun0gfzzw7ltmk7y68ks5ekqh4cpx9w, module_id: Main }
   Event: VaultCreationEvent {
     vault_id: NodeId(hex("5855080b052e56996aeedf0045f0ab8b2d6081e2bec248684ba4b5c2b03e")),
   }
├─ Emitter: Method { node: internal_vault_sim1tp2sszc99etfj6hwmuqytu9t3vkkpq0zhmpys6zt5j6u9vp7u6438n, module_id: Main }
   Event: DepositEvent {
     amount: Decimal("1000"),
   }
├─ Emitter: Method { node: internal_vault_sim1tz9uaalv8g3ahmwep2trlyj2m3zn7rstm9pwessa3k56me2fcduq2u, module_id: Main }
   Event: PayFeeEvent {
     amount: Decimal("0.65177966419"),
   }
├─ Emitter: Method { node: internal_vault_sim1tpsesv77qvw782kknjks9g3x2msg8cc8ldshk28pkf6m6lkhun3sel, module_id: Main }
   Event: DepositEvent {
     amount: Decimal("0.325889832095"),
   }
└─ Emitter: Method { node: resource_sim1tknxxxxxxxxxradxrdxxxxxxxxx009923554798xxxxxxxxxakj8n3, module_id: Main }
   Event: BurnFungibleResourceEvent {
     amount: Decimal("0.325889832095"),
   }
Outputs: 3
├─ Unit
├─ Reference("component_sim1crkp7q8sfhg7xa0xvqtdjezltj3hams2hrk4ztzqs2c90sy0cslv6a")
└─ Enum::[0]
Balance Changes: 3
├─ Vault: internal_vault_sim1tz9uaalv8g3ahmwep2trlyj2m3zn7rstm9pwessa3k56me2fcduq2u
   ResAddr: resource_sim1tknxxxxxxxxxradxrdxxxxxxxxx009923554798xxxxxxxxxakj8n3
   Change: -0.65177966419
├─ Vault: internal_vault_sim1tp2sszc99etfj6hwmuqytu9t3vkkpq0zhmpys6zt5j6u9vp7u6438n
   ResAddr: resource_sim1t4h3kupr5l95w6ufpuysl0afun0gfzzw7ltmk7y68ks5ekqh4cpx9w
   Change: 1000
└─ Vault: internal_vault_sim1tpsesv77qvw782kknjks9g3x2msg8cc8ldshk28pkf6m6lkhun3sel
   ResAddr: resource_sim1tknxxxxxxxxxradxrdxxxxxxxxx009923554798xxxxxxxxxakj8n3
   Change: 0.325889832095
New Entities: 2
└─ Component: component_sim1crkp7q8sfhg7xa0xvqtdjezltj3hams2hrk4ztzqs2c90sy0cslv6a
└─ Resource: resource_sim1t4h3kupr5l95w6ufpuysl0afun0gfzzw7ltmk7y68ks5ekqh4cpx9w
```

This reads as:

| Caption | Description |
| --- | --- |
| Transaction Status | Transaction was successful. |
| Transaction Cost | This sections describes the fees you had to pay to execute this transaction. Read [more about fees here](../../reference/radix-engine/costing-and-limits/README.md). |
| Logs | There were no logs. |
| Events | The transaction first executed a call to lock the fees. Then it executed one `CallFunction` instruction, that called the `instantiate_hello` function which in turn issued a `CallMethod` instruction. The events emitted show us what happened as a result of the manifest instructions. |
| Outputs and Balance Changes | The new instantiated `ComponentAddress` and information about resource movement is displayed here |
| New Entities | There were two new entities created: the `Hello` component and the `HelloToken` resource. |

* * *

This creates two new entities with different addresses:

-   a new `HelloToken` with a `ResourceAddress`,
-   and your fresh `Hello` component with a `ComponentAddress`. **Store the component address for later use**.

We have now completed all the development steps.

1.  Create a package.
2.  Define code with the business logic
3.  Deploy the code package to resim.
4.  Instantiate a component from the package.

## Using the `Hello` Component in `resim`

Now that we have our component in our local environment, we can interact with the it.

-   Let's call the `free_token` method.
    
```bash
    resim call-method <COMPONENT_ADDRESS> free_token
    ```
    

This uses `resim call-method` rather than the just used `resim call-function` since we are now calling a **method** on an instantiated component instead of a **function** on a blueprint.

**You can find an example and explanation of the method call output by clicking here**

The `free_token` output should look something like this:

```rust
Transaction Status: COMMITTED SUCCESS
Transaction Cost: 0.43160387771 XRD
├─ Network execution: 0.30021485 XRD, 6004297 execution cost units
├─ Network finalization: 0.0363077 XRD, 726154 finalization cost units
├─ Tip: 0 XRD
├─ Network Storage: 0.09508132771 XRD
└─ Royalties: 0 XRD
Logs: 1
└─ [INFO ] My balance is: 1000 HelloToken. Now giving away a token!
Events: 8
├─ Emitter: Method { node: internal_vault_sim1tz9uaalv8g3ahmwep2trlyj2m3zn7rstm9pwessa3k56me2fcduq2u, module_id: Main }
   Event: LockFeeEvent {
     amount: Decimal("5000"),
   }
├─ Emitter: Method { node: internal_vault_sim1tp2sszc99etfj6hwmuqytu9t3vkkpq0zhmpys6zt5j6u9vp7u6438n, module_id: Main }
   Event: WithdrawEvent {
     amount: Decimal("1"),
   }
├─ Emitter: Method { node: resource_sim1t4h3kupr5l95w6ufpuysl0afun0gfzzw7ltmk7y68ks5ekqh4cpx9w, module_id: Main }
   Event: VaultCreationEvent {
     vault_id: NodeId(hex("5825b3cabab992e4d46fda2ff9dad46a8b2021785ca645d0e2986f0824a2")),
   }
├─ Emitter: Method { node: internal_vault_sim1tqjm8j46hxfwf4r0mghlnkk5d29jqgtctjnyt58znphssf9zm6gyg5, module_id: Main }
   Event: DepositEvent {
     amount: Decimal("1"),
   }
├─ Emitter: Method { node: account_sim1c956qr3kxlgypxwst89j9yf24tjc7zxd4up38x37zr6q4jxdx9rhma, module_id: Main }
   Event: DepositEvent::Fungible(
     ResourceAddress(Reference("resource_sim1t4h3kupr5l95w6ufpuysl0afun0gfzzw7ltmk7y68ks5ekqh4cpx9w"),
     Decimal("1"),
   )
├─ Emitter: Method { node: internal_vault_sim1tz9uaalv8g3ahmwep2trlyj2m3zn7rstm9pwessa3k56me2fcduq2u, module_id: Main }
   Event: PayFeeEvent {
     amount: Decimal("0.43160387771"),
   }
├─ Emitter: Method { node: internal_vault_sim1tpsesv77qvw782kknjks9g3x2msg8cc8ldshk28pkf6m6lkhun3sel, module_id: Main }
   Event: DepositEvent {
     amount: Decimal("0.215801938855"),
   }
└─ Emitter: Method { node: resource_sim1tknxxxxxxxxxradxrdxxxxxxxxx009923554798xxxxxxxxxakj8n3, module_id: Main }
   Event: BurnFungibleResourceEvent {
     amount: Decimal("0.215801938855"),
   }
Outputs: 3
├─ Unit
├─ Own("internal_component_sim1lruex79e7m0tgvp852a0298xlmk5acltxnu8rp785yufcd26vcf5hg")
└─ Enum::[0]
Balance Changes: 4
├─ Vault: internal_vault_sim1tz9uaalv8g3ahmwep2trlyj2m3zn7rstm9pwessa3k56me2fcduq2u
   ResAddr: resource_sim1tknxxxxxxxxxradxrdxxxxxxxxx009923554798xxxxxxxxxakj8n3
   Change: -0.43160387771
├─ Vault: internal_vault_sim1tp2sszc99etfj6hwmuqytu9t3vkkpq0zhmpys6zt5j6u9vp7u6438n
   ResAddr: resource_sim1t4h3kupr5l95w6ufpuysl0afun0gfzzw7ltmk7y68ks5ekqh4cpx9w
   Change: -1
├─ Vault: internal_vault_sim1tqjm8j46hxfwf4r0mghlnkk5d29jqgtctjnyt58znphssf9zm6gyg5
   ResAddr: resource_sim1t4h3kupr5l95w6ufpuysl0afun0gfzzw7ltmk7y68ks5ekqh4cpx9w
   Change: 1
└─ Vault: internal_vault_sim1tpsesv77qvw782kknjks9g3x2msg8cc8ldshk28pkf6m6lkhun3sel
   ResAddr: resource_sim1tknxxxxxxxxxradxrdxxxxxxxxx009923554798xxxxxxxxxakj8n3
   Change: 0.215801938855
New Entities: 0
```

This reads as:

| Caption | Description |
| --- | --- |
| Transaction status | Transaction was successful. |
| Transaction Fee and Cost Units | This sections describes the fees you had to pay to execute this transaction. Read [more about fees here](../../reference/radix-engine/costing-and-limits/README.md). |
| Logs | There was one log message. The message was: "\[INFO \] My balance is: 1000 HelloToken. Now giving away a token!" |
| Events | The transaction first locked the fees. Then it executed one `CallMethod` instruction, that called the `free_token` method which in turn issued another `CallMethod` instruction to withdraw and store the `HelloToken` in your account. |
| Outputs and Balance Changes | Information about resource movement is displayed here |

* * *

You'll now have a shiny new `HelloToken` in your account, and your `Hello` component has one less.

-   We can check this by using `resim show` on our account and `Hello` components.
    
```bash
    resim show <ACCOUNT_ADDRESS>
    ```
    
```bash
    resim show <COMPONENT_ADDRESS>
    ```
    

## Final Notes

-   If you make changes to the structs within your code, then unfortunately you will have to run through the entire publish-instantiate-call flow from scratch, saving the new addresses as they appear. If you only make implementation changes then it is possible to update your package with:
    
```bash
    resim publish . --package-address <PACKAGE_ADDRESS>
    ```
    
-   At any point you can instantly get a clean slate in the simulator by running:
    
```bash
    resim reset
    ```
    
    You almost certainly need to do this if you switch to working on a different project.
    

Well done. You've finished this introductory tutorial on using `resim` for Scrypto. You can continuing your learning journey with our explanation of the package code in the next section, [Explain Your First Scrypto Project](learning-to-explain-your-first-scrypto-project.md).

Let us know if you find any section helpful or not by clicking one of the buttons below ⬇. You can also let us know about a typo or outdated information using the same buttons.