---
title: "Assign Component Royalty Roles"
---
# Assign Component Royalty Roles

### Assign AccessRules for Component Royalty Roles

Components may optionally include Component Royalties. If so, a set of component royalty roles will be defined for that component:

<table width="100%" class="editor360-table" borderstyle="solid"><tbody><tr><td colspan="1" rowspan="1" colwidth="244" data-background-color="#ffffff" style="width:244px;background-color: #ffffff;"><p><strong>Role</strong></p></td><td colspan="1" rowspan="1" colwidth="481" data-background-color="#ffffff" style="width:481px;background-color: #ffffff;"><p><strong>Authority Description</strong></p></td><td colspan="1" rowspan="1" style=""><p><strong>Methods Accessible</strong></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>royalty_setter</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Update royalty amount for a method</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">ComponentRoyalty::set_royalty</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>royalty_setter_updater</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Update the AccessRule of the royalty_setter</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">RoleAssignment::set(ModuleId::Royalty, "royalty_setter", ..)</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>royalty_locker</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Lock the royalty amount for a method such that they are no longer updateable</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">ComponentRoyalty::lock_royalty</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>royalty_locker_updater</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Update the AccessRule of the royalty_locker</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">RoleAssignment::set(ModuleId::Royalty, "royalty_locker", ..)</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>royalty_claimer</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Withdraw the royalties accumulated by the component</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">ComponentRoyalty::claim_royalties</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>royalty_claimer_updater</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Update the AccessRule of the royalty_claimer</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">RoleAssignment::set(ModuleId::Royalty, "royalty_claimer", ..)</code></p></td></tr></tbody></table>

By default, the Owner Role will inherit all Metadata Roles.

### Assign Custom AccessRules for Component Royalty Roles

If custom access rules for each component royalty role are required (rather than the Owner role inheriting all roles) then add `roles` to the `component_royalties!` macro during component globalization:

```rust
#[blueprint]
mod my_token_sale {
    enable_method_auth! {
        roles {
            super_admin_role => updatable_by: [];
            admin_role => updatable_by: [super_admin_role];
        },
        methods { .. }
    }

    struct MyTokenSale { .. }
    
    impl MyTokenSale {
        pub fn create() {
            let owner_badge: Bucket = { .. };
            let owner_access_rule: AccessRule = { .. };
            let royalty_setter_access_rule: AccessRule = { .. };
            let royalty_locker_access_rule: AccessRule = { .. };
            let royalty_locker_updater_access_rule: AccessRule = { .. };

            MyTokenSale { .. }
                .instantiate()
                .prepare_to_globalize(OwnerRole::Fixed(owner_access_rule))
                .enable_component_royalties(component_royalties! {
                    roles {
                        royalty_setter => royalty_setter_access_rule; // #1
                        royalty_setter_updater => rule!(deny_all); // #2
                        royalty_locker => royalty_locker_access_rule;
                        royalty_locker_updater => royalty_locker_updater_access_rule;
                        royalty_claimer => OWNER; // #3
                        royalty_claimer_updater => OWNER;
                    },
                    init { .. }
                })
                .globalize()

          ..
        }
        ..
    }
}
```

1.  The `royalty_setter_access_rule` is assigned to the `royalty_setter` role
    
2.  The `royaty_setter_updater` role is not accessible by anyone effectively “locking” in the AccessRule of `royalty_setter`
    
3.  Using `OWNER` specifies that the owner role will inherit the `royalty_claimer` role