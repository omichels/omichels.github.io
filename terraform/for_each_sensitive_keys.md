Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.

$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.write-azure-key-vault-secrets.azurerm_key_vault_secret.this["key123"] will be created
  + resource "azurerm_key_vault_secret" "this" {
      + id                      = (known after apply)
      + key_vault_id            = "/subscriptions/964df7ca-yyyy-0000-xxxx-1ed9db5723f8/resourceGroups/1-45e433a8-playground-sandbox/providers/Microsoft.KeyVault/vaults/captain-k8s-key-vault"
      + name                    = "key123"
      + resource_id             = (known after apply)
      + resource_versionless_id = (known after apply)
      + value                   = (sensitive value)
      + version                 = (known after apply)
      + versionless_id          = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────


