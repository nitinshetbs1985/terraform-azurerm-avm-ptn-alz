<!-- BEGIN_TF_DOCS -->
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/Azure/terraform-azurerm-avm-ptn-alz/badge)](https://scorecard.dev/viewer/?uri=github.com/Azure/terraform-azurerm-avm-ptn-alz)

# ALZ Terraform Module

- This repository contains a Terraform module for deploying Azure Landing Zones (ALZs).
- Make sure to review the examples.

> [!IMPORTANT]
> Make sure to add `.alzlib` to your `.gitignore` file to avoid committing the downloaded ALZ library to your repository.

## Features

- Deploy management groups according to the supplied architecture (defaut is ALZ)
- Deploy policy assets (definitions, assignments, and initiatives) according to the supplied architecture ands associated archetyes
- Modify policy assignments:
  - Enforcement mode
  - Identity
  - Non-compliance messages
  - Overrides
  - Parameters
  - Resource selectors
- Create the required role assignments for Azure Policy, including support for the **assign permissions** metadata tag, just like the Azure Portal
- Deploy custom role definitions

## Unknown Values

This module uses the ALZ Terraform provider. This uses a data source which **must** be read prior to creating the plan.
If you pass an unknown (known after apply) value into the module, it will not be able to read the data source until the plan is being applied.
This may cause resources to be unnecessarily recreated.

Such unknown values include resource ids. For example, if you are creating a resource and passing the id of the resource group to the module, this will cause the issue.

Instead, use string interpolation to pass the values. For example:

### Recommended

This is the recommended way to use this module:

> [!NOTE]
> We assume that all variable inputs are literals.

```terraform

locals {
  foo_resource_id = "/subscriptions/${data.azurerm_client_config.current.subscription_id}/resourceGroups/${var.resource_group_name}/providers/Microsoft.FooResourceProvider/${var.foo_resource_name}"
}

module "example" {
  source = "Azure/terraform-azurerm-avm-ptn-alz/azurerm"

  policy_assignments_to_modify = {
    alzroot = {
      policy_assignments = {
        mypolicy = {
          parameters = {
            parameterName = jsonencode({ value = local.foo_resource_id })
          }
        }
      }
    }
  }
}
```

### Deferred Actions

We are awaiting the results of the upstream Terraform language experiment *deferred actions*. This may provide a solution to this issue.
See the release notes [here](https://github.com/hashicorp/terraform/releases/tag/v1.10.0-alpha20240619) for more information.

<!-- markdownlint-disable MD033 -->
## Requirements

The following requirements are needed by this module:

- <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) (~> 1.9)

- <a name="requirement_alz"></a> [alz](#requirement\_alz) (~> 0.14)

- <a name="requirement_azapi"></a> [azapi](#requirement\_azapi) (2.0.0-beta)

- <a name="requirement_modtm"></a> [modtm](#requirement\_modtm) (~> 0.3)

- <a name="requirement_random"></a> [random](#requirement\_random) (~> 3.6)

- <a name="requirement_time"></a> [time](#requirement\_time) (~> 0.9)

## Resources

The following resources are used by this module:

- [azapi_resource.hierarchy_settings](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.management_groups_level_0](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.management_groups_level_1](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.management_groups_level_2](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.management_groups_level_3](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.management_groups_level_4](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.management_groups_level_5](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.management_groups_level_6](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.policy_assignments](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.policy_definitions](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.policy_role_assignments](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.policy_set_definitions](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.role_definitions](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_resource.subscription_placement](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/resource) (resource)
- [azapi_update_resource.hierarchy_settings](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/resources/update_resource) (resource)
- [modtm_telemetry.telemetry](https://registry.terraform.io/providers/azure/modtm/latest/docs/resources/telemetry) (resource)
- [random_uuid.telemetry](https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/uuid) (resource)
- [time_sleep.after_management_groups](https://registry.terraform.io/providers/hashicorp/time/latest/docs/resources/sleep) (resource)
- [time_sleep.after_policy_definitions](https://registry.terraform.io/providers/hashicorp/time/latest/docs/resources/sleep) (resource)
- [time_sleep.after_policy_set_definitions](https://registry.terraform.io/providers/hashicorp/time/latest/docs/resources/sleep) (resource)
- [alz_architecture.this](https://registry.terraform.io/providers/azure/alz/latest/docs/data-sources/architecture) (data source)
- [azapi_client_config.hierarchy_settings](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/data-sources/client_config) (data source)
- [azapi_client_config.telemetry](https://registry.terraform.io/providers/azure/azapi/2.0.0-beta/docs/data-sources/client_config) (data source)
- [modtm_module_source.telemetry](https://registry.terraform.io/providers/azure/modtm/latest/docs/data-sources/module_source) (data source)

<!-- markdownlint-disable MD013 -->
## Required Inputs

The following input variables are required:

### <a name="input_architecture_name"></a> [architecture\_name](#input\_architecture\_name)

Description: The name of the architecture to create. This needs to be of the `*.alz_architecture_definition.[json|yaml|yml]` files.

Type: `string`

### <a name="input_location"></a> [location](#input\_location)

Description: The default location for resources in this management group. Used for policy managed identities.

Type: `string`

### <a name="input_parent_resource_id"></a> [parent\_resource\_id](#input\_parent\_resource\_id)

Description: The resource id of the parent management group. Use the tenant id to create a child of the tenant root group.  
The `azurerm_client_config`/`azapi_client_config` data sources are able to retrieve the tenant id.

Type: `string`

## Optional Inputs

The following input variables are optional (have default values):

### <a name="input_delays"></a> [delays](#input\_delays)

Description: DEPRECATED: Please use the new `retries` variable instead to allow the provider to retry on certain errors.

A map of delays to apply to the creation and destruction of resources.  
Included to work around some race conditions in Azure.

Type:

```hcl
object({
    after_management_group = optional(object({
      create  = optional(string, "0s")
      destroy = optional(string, "0s")
    }), {})
    after_policy_definitions = optional(object({
      create  = optional(string, "0s")
      destroy = optional(string, "0s")
    }), {})
    after_policy_set_definitions = optional(object({
      create  = optional(string, "0s")
      destroy = optional(string, "0s")
    }), {})
  })
```

Default: `{}`

### <a name="input_enable_telemetry"></a> [enable\_telemetry](#input\_enable\_telemetry)

Description: This variable controls whether or not telemetry is enabled for the module.  
For more information see https://aka.ms/avm/telemetryinfo.  
If it is set to false, then no telemetry will be collected.

Type: `bool`

Default: `true`

### <a name="input_management_group_hierarchy_settings"></a> [management\_group\_hierarchy\_settings](#input\_management\_group\_hierarchy\_settings)

Description: Set this value to configure the hierarchy settings. Options are:

- `default_management_group_name` - (Required) The name of the default management group.
- `require_authorization_for_group_creation` - (Optional) By default, all Entra security principals can create new management groups. When enabled, security principals must have management group write access to create new management groups. Defaults to `true`.
- `update_existing` - (Optional) Update existing hierarchy settings rather than create new. Defaults to `false`.

Type:

```hcl
object({
    default_management_group_name            = string
    require_authorization_for_group_creation = optional(bool, true)
    update_existing                          = optional(bool, false)
  })
```

Default: `null`

### <a name="input_partner_id"></a> [partner\_id](#input\_partner\_id)

Description: A value to be included in the telemetry tag. Requires the `enable_telemetry` variable to be set to `true`. The must be in the following format:

`<PARTNER_ID_UUID>:<PARTNER_DATA_UUID>`

e.g.

`00000000-0000-0000-0000-000000000000:00000000-0000-0000-0000-000000000000`

Type: `string`

Default: `null`

### <a name="input_policy_assignments_to_modify"></a> [policy\_assignments\_to\_modify](#input\_policy\_assignments\_to\_modify)

Description: A map of policy assignment objects to modify the ALZ architecture with.  
You only need to specify the properties you want to change.

The key is the id of the management group. The value is an object with a single attribute, `policy_assignments`.  
The `policy_assignments` value is a map of policy assignments to modify.  
The key of this map is the assignment name, and the value is an object with optional attributes for modifying the policy assignments.

- `enforcement_mode` - (Optional) The enforcement mode of the policy assignment. Possible values are `Default` and `DoNotEnforce`.
- `identity` - (Optional) The identity of the policy assignment. Possible values are `SystemAssigned` and `UserAssigned`.
- `identity_ids` - (Optional) A set of ids of the user assigned identities to assign to the policy assignment.
- `non_compliance_message` - (Optional) A set of non compliance message objects to use for the policy assignment. Each object has the following properties:
  - `message` - (Required) The non compliance message.
  - `policy_definition_reference_id` - (Optional) The reference id of the policy definition to use for the non compliance message.
- `parameters` - (Optional) The parameters to use for the policy assignment. The map key is the parameter name and the value is an JSON object containing a single `Value` attribute with the values to apply. This to mitigate issues with the Terraform type system. E.g. `{ defaultName = jsonencode({Value = \"value\"}) }`.
- `resource_selectors` - (Optional) A list of resource selector objects to use for the policy assignment. Each object has the following properties:
  - `name` - (Required) The name of the resource selector.
  - `selectors` - (Optional) A list of selector objects to use for the resource selector. Each object has the following properties:
    - `kind` - (Required) The kind of the selector. Allowed values are: `resourceLocation`, `resourceType`, `resourceWithoutLocation`. `resourceWithoutLocation` cannot be used in the same resource selector as `resourceLocation`.
    - `in` - (Optional) A set of strings to include in the selector.
    - `not_in` - (Optional) A set of strings to exclude from the selector.
- `overrides` - (Optional) A list of override objects to use for the policy assignment. Each object has the following properties:
  - `kind` - (Required) The kind of the override.
  - `value` - (Required) The value of the override. Supported values are policy effects: <https://learn.microsoft.com/azure/governance/policy/concepts/effects>.
  - `selectors` - (Optional) A list of selector objects to use for the override. Each object has the following properties:
    - `kind` - (Required) The kind of the selector.
    - `in` - (Optional) A set of strings to include in the selector.
    - `not_in` - (Optional) A set of strings to exclude from the selector.

Type:

```hcl
map(object({
    policy_assignments = map(object({
      enforcement_mode = optional(string, null)
      identity         = optional(string, null)
      identity_ids     = optional(list(string), null)
      parameters       = optional(map(string), null)
      non_compliance_messages = optional(set(object({
        message                        = string
        policy_definition_reference_id = optional(string, null)
      })), null)
      resource_selectors = optional(list(object({
        name = string
        selectors = optional(list(object({
          kind   = string
          in     = optional(set(string), null)
          not_in = optional(set(string), null)
        })), [])
      })))
      overrides = optional(list(object({
        kind  = string
        value = string
        selectors = optional(list(object({
          kind   = string
          in     = optional(set(string), null)
          not_in = optional(set(string), null)
        })), [])
      })))
    }))
  }))
```

Default: `{}`

### <a name="input_policy_default_values"></a> [policy\_default\_values](#input\_policy\_default\_values)

Description: A map of default values to apply to policy assignments. The key is the default name as defined in the library, and the value is an JSON object containing a single `value` attribute with the values to apply. This to mitigate issues with the Terraform type system. E.g. `{ defaultName = jsonencode({ value = \"value\"}) }`

Type: `map(string)`

Default: `null`

### <a name="input_retries"></a> [retries](#input\_retries)

Description: The retry settings to apply to the CRUD operations. Value is a nested object, the top level keys are the resources and the values are an object with the following attributes:

- `error_message_regex` - (Optional) A list of error message regexes to retry on. Defaults to `null`, which will will disable retries. Specify a value to enable.
- `interval_seconds` - (Optional) The initial interval in seconds between retries. Defaults to `null` and will fall back to the provider default value.
- `max_interval_seconds` - (Optional) The maximum interval in seconds between retries. Defaults to `null` and will fall back to the provider default value.
- `multiplier` - (Optional) The multiplier to apply to the interval between retries. Defaults to `null` and will fall back to the provider default value.
- `randomization_factor` - (Optional) The randomization factor to apply to the interval between retries. Defaults to `null` and will fall back to the provider default value.

For more information please see the provider documentation here: <https://registry.terraform.io/providers/Azure/azapi/azurerm/latest/docs/resources/resource#nestedatt--retry>

Type:

```hcl
object({
    management_groups = optional(object({
      error_message_regex = optional(list(string), [
        "AuthorizationFailed" # Avoids a eventual consistency issue where a recently created management group is not yet available for a GET operation.
      ])
      interval_seconds     = optional(number, null)
      max_interval_seconds = optional(number, null)
      multiplier           = optional(number, null)
      randomization_factor = optional(number, null)
    }), {})
    role_definitions = optional(object({
      error_message_regex  = optional(list(string), null)
      interval_seconds     = optional(number, null)
      max_interval_seconds = optional(number, null)
      multiplier           = optional(number, null)
      randomization_factor = optional(number, null)
    }), {})
    policy_definitions = optional(object({
      error_message_regex  = optional(list(string), null)
      interval_seconds     = optional(number, null)
      max_interval_seconds = optional(number, null)
      multiplier           = optional(number, null)
      randomization_factor = optional(number, null)
    }), {})
    policy_set_definitions = optional(object({
      error_message_regex  = optional(list(string), null)
      interval_seconds     = optional(number, null)
      max_interval_seconds = optional(number, null)
      multiplier           = optional(number, null)
      randomization_factor = optional(number, null)
    }), {})
    policy_assignments = optional(object({
      error_message_regex = optional(list(string), [
        "The policy definition specified in policy assignment '.+' is out of scope" # If assignment is created soon after a policy definition has been created then the assignment will fail with this error.
      ])
      interval_seconds     = optional(number, 5)
      max_interval_seconds = optional(number, 30)
      multiplier           = optional(number, null)
      randomization_factor = optional(number, null)
    }), {})
    policy_role_assignments = optional(object({
      error_message_regex = optional(list(string), [
        "RoleAssignmentNotFound" # Added to fix an eventual consistency error with a GET following soon after a PUT
      ])
      interval_seconds     = optional(number, null)
      max_interval_seconds = optional(number, null)
      multiplier           = optional(number, null)
      randomization_factor = optional(number, null)
    }), {})
    hierarchy_settings = optional(object({
      error_message_regex  = optional(list(string), null)
      interval_seconds     = optional(number, null)
      max_interval_seconds = optional(number, null)
      multiplier           = optional(number, null)
      randomization_factor = optional(number, null)
    }), {})
    subscription_placement = optional(object({
      error_message_regex  = optional(list(string), null)
      interval_seconds     = optional(number, null)
      max_interval_seconds = optional(number, null)
      multiplier           = optional(number, null)
      randomization_factor = optional(number, null)
    }), {})
  })
```

Default: `{}`

### <a name="input_subscription_placement"></a> [subscription\_placement](#input\_subscription\_placement)

Description: A map of subscriptions to place into management groups. The key is deliberately arbitrary to avoid issues with known after apply values. The value is an object:

- `subscription_id` - (Required) The id of the subscription to place in the management group.
- `management_group_name` - (Required) The name of the management group to place the subscription in.

Type:

```hcl
map(object({
    subscription_id       = string
    management_group_name = string
  }))
```

Default: `{}`

### <a name="input_timeouts"></a> [timeouts](#input\_timeouts)

Description: A map of timeouts to apply to the creation and destruction of resources.

Type:

```hcl
object({
    management_group = optional(object({
      create = optional(string, "5m")
      delete = optional(string, "5m")
      update = optional(string, "5m")
      read   = optional(string, "5m")
      }), {}
    )
    role_definition = optional(object({
      create = optional(string, "5m")
      delete = optional(string, "5m")
      update = optional(string, "5m")
      read   = optional(string, "5m")
      }), {}
    )
    policy_definition = optional(object({
      create = optional(string, "5m")
      delete = optional(string, "5m")
      update = optional(string, "5m")
      read   = optional(string, "5m")
      }), {}
    )
    policy_set_definition = optional(object({
      create = optional(string, "5m")
      delete = optional(string, "5m")
      update = optional(string, "5m")
      read   = optional(string, "5m")
      }), {}
    )
    policy_assignment = optional(object({
      create = optional(string, "15m") # Set high to allow consolidation of policy definitions coming into scope
      delete = optional(string, "5m")
      update = optional(string, "5m")
      read   = optional(string, "5m")
      }), {}
    )
    policy_role_assignment = optional(object({
      create = optional(string, "5m")
      delete = optional(string, "5m")
      update = optional(string, "5m")
      read   = optional(string, "5m")
      }), {}
    )
  })
```

Default: `{}`

## Outputs

The following outputs are exported:

### <a name="output_management_group_resource_ids"></a> [management\_group\_resource\_ids](#output\_management\_group\_resource\_ids)

Description: A map of management group names to their resource ids.

### <a name="output_policy_assignment_identity_ids"></a> [policy\_assignment\_identity\_ids](#output\_policy\_assignment\_identity\_ids)

Description: A map of policy assignment names to their identity ids.

### <a name="output_policy_assignment_resource_ids"></a> [policy\_assignment\_resource\_ids](#output\_policy\_assignment\_resource\_ids)

Description: A map of policy assignment names to their resource ids.

### <a name="output_policy_definition_resource_ids"></a> [policy\_definition\_resource\_ids](#output\_policy\_definition\_resource\_ids)

Description: A map of policy definition names to their resource ids.

### <a name="output_policy_role_assignment_resource_ids"></a> [policy\_role\_assignment\_resource\_ids](#output\_policy\_role\_assignment\_resource\_ids)

Description: A map of policy role assignments to their resource ids.

### <a name="output_policy_set_definition_resource_ids"></a> [policy\_set\_definition\_resource\_ids](#output\_policy\_set\_definition\_resource\_ids)

Description: A map of policy set definition names to their resource ids.

### <a name="output_role_definition_resource_ids"></a> [role\_definition\_resource\_ids](#output\_role\_definition\_resource\_ids)

Description: A map of role definition names to their resource ids.

## Modules

No modules.

<!-- markdownlint-disable-next-line MD041 -->
## Data Collection

The software may collect information about you and your use of the software and send it to Microsoft. Microsoft may use this information to provide services and improve our products and services. You may turn off the telemetry as described in the repository. There are also some features in the software that may enable you and Microsoft to collect data from users of your applications. If you use these features, you must comply with applicable law, including providing appropriate notices to users of your applications together with a copy of Microsoft’s privacy statement. Our privacy statement is located at <https://go.microsoft.com/fwlink/?LinkID=824704>. You can learn more about data collection and use in the help documentation and our privacy statement. Your use of the software operates as your consent to these practices.
<!-- END_TF_DOCS -->