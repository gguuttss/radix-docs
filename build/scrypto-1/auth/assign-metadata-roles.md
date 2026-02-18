---
title: "Assign Metadata Roles"
---
# Assign Metadata Roles

### Assign AccessRules for Metadata Roles

Every component has Metadata and a set of Metadata Roles:

<table width="100%" class="editor360-table" borderstyle="solid"><tbody><tr><td colspan="1" rowspan="1" colwidth="244" data-background-color="#ffffff" style="width:244px;background-color: #ffffff;"><p><strong>Role</strong></p></td><td colspan="1" rowspan="1" colwidth="481" data-background-color="#ffffff" style="width:481px;background-color: #ffffff;"><p><strong>Authority Description</strong></p></td><td colspan="1" rowspan="1" style=""><p><strong>Methods Accessible</strong></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>metadata_setter</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Update a metadata entry</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">Metadata::set(..)</code></p><p><code dir="">Metadata::remove(..)</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>metadata_setter_updater</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Update the AccessRule of the metadata_setter</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">RoleAssignment::set(ModuleId::Metadata, "metadata_setter", ..)</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>metadata_locker</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Lock metadata entries such that they are no longer updateable</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">Metadata::lock(..)</code></p></td></tr><tr><td colspan="1" rowspan="1" colwidth="244" style="width:244px;"><p>metadata_locker_updater</p></td><td colspan="1" rowspan="1" colwidth="481" style="width:481px;"><p>Update the AccessRule of the metadata_locker</p></td><td colspan="1" rowspan="1" style=""><p><code dir="">RoleAssignment::set(ModuleId::Metadata, "metadata_locker", ..)</code></p></td></tr></tbody></table>

By default, the Owner Role will inherit all Metadata Roles.

### Assign Custom AccessRules for Metadata Roles

If custom access rules for each metadata role are required (rather than the Owner role inheriting all roles) then add `roles` to the `metadata!` macro during component globalization:

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
            let metadata_setter_access_rule: AccessRule = { .. };

            MyTokenSale { .. }
                .instantiate()
                .prepare_to_globalize(OwnerRole::Fixed(owner_access_rule))
                .metadata(metadata! {
                    roles {
                        metadata_setter => metadata_setter_access_rule.clone(); // #1
                        metadata_setter_updater => metadata_setter_access_rule;
                        metadata_locker => OWNER; // #2
                        metadata_locker_updater => rule!(deny_all); // #3
                    }
                })
                .globalize()

          ..
        }
        ..
    }
}
```

1.  The `metadata_setter_access_rule` is assigned to the `metadata_setter` role
    
2.  Using `OWNER` specifies that the owner role will inherit the `metadata_locker` role
    
3.  The `metadata_locker_updater` role is not accessible by anyone effectively “locking” in the AccessRule of `metadata_locker`
    

###