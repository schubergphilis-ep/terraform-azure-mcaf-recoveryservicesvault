# Upgrading

## Upgrading to v1.0.0

v1.0.0 has three breaking changes. Check all three:

1. [Requires azurerm v5](#requires-azurerm-v5): update the provider constraint and run `terraform init -upgrade`.
2. [Removed input `soft_delete_enabled`](#removed-input-soft_delete_enabled): remove it from your module call.
3. [`cmk_key_vault_key_id` is now `cmk_key_vault_key`](#cmk_key_vault_key_id-is-now-cmk_key_vault_key): pass the key URL and the key's resource ID if you use customer-managed keys.

None of these change resource addresses in the state, so no `moved` blocks are needed.

### Requires azurerm v5

The module and its submodules now require `hashicorp/azurerm` `>= 5` (it was `>= 4, < 5.0`).
The module no longer works with azurerm v4. The resources it manages
(`azurerm_recovery_services_vault`, `azurerm_backup_policy_vm`, `azurerm_backup_policy_file_share`
and `azurerm_role_assignment`) have the same arguments in v4 and v5, apart from the removed
`soft_delete_enabled` below.

**What you need to do:**

1. Set the provider constraint in your root module to `>= 5`:

   ```hcl
   terraform {
     required_providers {
       azurerm = {
         source  = "hashicorp/azurerm"
         version = ">= 5"
       }
     }
   }
   ```

2. Run `terraform init -upgrade`. If you commit `.terraform.lock.hcl`, commit the updated file.
3. Run `terraform plan` and review it. The v5 provider can also change other resources in your
   configuration; see the
   [azurerm v5 upgrade guide](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/guides/5.0-upgrade-guide).

### Removed input `soft_delete_enabled`

`soft_delete_enabled` was deprecated in azurerm v4 and is gone from `azurerm_recovery_services_vault`
in v5, so the module can't set it any more. Terraform fails with *"Unsupported argument"* if you
still pass it.

**What you need to do:**

| Your configuration on v0.x | Action |
|---|---|
| `soft_delete_enabled` not set | Nothing. |
| `soft_delete_enabled = true` | Remove the argument from your module call. |
| `soft_delete_enabled = false` | Remove the argument from your module call. The module no longer sets soft delete on the vault, so check the vault's soft delete setting in Azure if you rely on it being off. |

### `cmk_key_vault_key_id` is now `cmk_key_vault_key`

#### What changed

v0.x used one string for two things that need different formats: the key URL that the vault
encrypts with (`key_id`), and the scope of the *Key Vault Crypto Service Encryption User* role
assignment, which must be an Azure resource ID. The module now takes an object with both:

| Attribute | Value |
|---|---|
| `id` | The key URL, as you passed to `cmk_key_vault_key_id` before. |
| `resource_id` | The Azure resource ID of the key. Used as the scope of the role assignment. |

`cmk_key_vault_key` is optional as a whole: leave it unset (`null`) and the vault uses no
customer-managed key. If you set it, both attributes are required, also when
`system_assigned_identity_enabled = false` and the module creates no role assignment.

#### Am I affected?

Only if you set `cmk_key_vault_key_id`. Run in your configuration:

```bash
grep -rn 'cmk_key_vault_key_id' .
```

- **No output**: nothing to do for this change.
- **Output**: replace the input as described below.

#### How to upgrade

1. Replace the input. With an `azurerm_key_vault_key`:

   ```hcl
   # v0.x
   cmk_key_vault_key_id = azurerm_key_vault_key.this.id

   # v1.0.0
   cmk_key_vault_key = {
     id          = azurerm_key_vault_key.this.id
     resource_id = azurerm_key_vault_key.this.resource_id
   }
   ```

   Without the resource, use the key URL (`https://<vault>.vault.azure.net/keys/<key>`) for `id`
   and the key's Azure resource ID
   (`/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.KeyVault/vaults/<vault>/keys/<key>`)
   for `resource_id`.
2. Run `terraform plan`. The vault's encryption doesn't change when `id` equals the value you
   passed before. The role assignment (`azurerm_role_assignment.this[0]`, only created with
   `system_assigned_identity_enabled = true`) doesn't change either when `resource_id` equals its
   current scope. If it differs, the plan replaces the role assignment, as the scope of a role
   assignment can't be changed in place.
3. Apply.
