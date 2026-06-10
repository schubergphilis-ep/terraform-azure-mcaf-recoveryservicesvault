<!-- BEGIN_TF_DOCS -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 1.9 |
| <a name="requirement_azurerm"></a> [azurerm](#requirement\_azurerm) | >= 4, < 5.0 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_azurerm"></a> [azurerm](#provider\_azurerm) | >= 4, < 5.0 |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [azurerm_backup_policy_file_share.this](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/backup_policy_file_share) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_recovery_services_vault_name"></a> [recovery\_services\_vault\_name](#input\_recovery\_services\_vault\_name) | The name of the Recovery Services Vault. | `string` | n/a | yes |
| <a name="input_resource_group_name"></a> [resource\_group\_name](#input\_resource\_group\_name) | The name of the resource group in which the Recovery Services Vault should be created. | `string` | n/a | yes |
| <a name="input_file_share_backup_policy"></a> [file\_share\_backup\_policy](#input\_file\_share\_backup\_policy) | A map objects for backup and retation options.<br/><br/>    - `name` - (Optional) The name of the private endpoint. One will be generated if not set.<br/>    - `role_assignments` - (Optional) A map of role assignments to create on the <br/><br/>    - `backup` - (required) backup options.<br/>        - `frequency` - (Required) Sets the backup frequency. Possible values are hourly, Daily and Weekly.<br/>        - `time` - (required) Specify time in a 24 hour format HH:MM. "22:00"<br/>        - `hour_interval` - (Optional) Interval in hour at which backup is triggered. Possible values are 4, 6, 8 and 12. This is used when frequency is hourly. 6<br/>        - `hour_duration` -  (Optional) Duration of the backup window in hours. Possible values are between 4 and 24 This is used when frequency is hourly. 12<br/>        - `weekdays` -  (Optional) The days of the week to perform backups on. Must be one of Sunday, Monday, Tuesday, Wednesday, Thursday, Friday or Saturday. This is used when frequency is Weekly. ["Tuesday", "Saturday"]<br/>    - `retention_daily` - (Optional)<br/>      - `count` - <br/>    - `retantion_weekly` -<br/>      - `count` -<br/>      - `weekdays` -<br/>    - `retantion_monthly` -<br/>      - `count` -  # (Required) The number of monthly backups to keep. Must be between 1 and 9999<br/>      - `weekdays` - (Optional) The weekday backups to retain . Must be one of Sunday, Monday, Tuesday, Wednesday, Thursday, Friday or Saturday.<br/>      - `weeks` -  # (Optional) The weeks of the month to retain backups of. Must be one of First, Second, Third, Fourth, Last.<br/>      - `days` -  # (Optional) The days of the month to retain backups of. Must be between 1 and 31.<br/>      - `include_last_days` -  # (Optional) Including the last day of the month, default to false.<br/>    - `retantion_yearly` -<br/>      - `months` - # (Required) The months of the year to retain backups of. Must be one of January, February, March, April, May, June, July, August, September, October, November and December.<br/>      - `count` -  # (Required) The number of monthly backups to keep. Must be between 1 and 9999<br/>      - `weekdays` - (Optional) The weekday backups to retain . Must be one of Sunday, Monday, Tuesday, Wednesday, Thursday, Friday or Saturday.<br/>      - `weeks` -  # (Optional) The weeks of the month to retain backups of. Must be one of First, Second, Third, Fourth, Last.<br/>      - `days` -  # (Optional) The days of the month to retain backups of. Must be between 1 and 31.<br/>      - `include_last_days` -  # (Optional) Including the last day of the month, default to false.<br/><br/>    example:<br/>      retentions = {<br/>      rest1 = {<br/>        backup = {<br/>          frequency     = "hourly"<br/>          time          = "22:00"<br/>          hour\_interval = 6<br/>          hour\_duration = 12<br/>          # weekdays      = ["Tuesday", "Saturday"]<br/>        }<br/>        retention\_daily = 7<br/>        retention\_weekly = {<br/>          count    = 7<br/>          weekdays = ["Monday", "Wednesday"]<br/><br/>        }<br/>        retention\_monthly = {<br/>          count = 5<br/>          # weekdays =  ["Tuesday","Saturday"]<br/>          # weeks = ["First","Third"]<br/>          days = [3, 10, 20]<br/>        }<br/>        retention\_yearly = {<br/>          count  = 5<br/>          months = []<br/>          # weekdays =  ["Tuesday","Saturday"]<br/>          # weeks = ["First","Third"]<br/>          days = [3, 10, 20]<br/>        }<br/><br/>        }<br/>      } | <pre>object({<br/>    name      = string<br/>    timezone  = string<br/>    frequency = string<br/><br/>    retention_daily = optional(number, null)<br/><br/>    backup = object({<br/>      time = string<br/>      hourly = optional(object({<br/>        interval        = number<br/>        start_time      = string<br/>        window_duration = number<br/>      }))<br/>    })<br/><br/>    retention_weekly = optional(object({<br/>      count    = optional(number, 7)<br/>      weekdays = optional(list(string), [])<br/>    }), {})<br/><br/>    retention_monthly = optional(object({<br/>      count             = optional(number, 0)<br/>      weekdays          = optional(list(string), [])<br/>      weeks             = optional(list(string), [])<br/>      days              = optional(list(number), [])<br/>      include_last_days = optional(bool, false)<br/>    }), {})<br/><br/>    retention_yearly = optional(object({<br/>      count             = optional(number, 0)<br/>      months            = optional(list(string), [])<br/>      weekdays          = optional(list(string), [])<br/>      weeks             = optional(list(string), [])<br/>      days              = optional(list(number), [])<br/>      include_last_days = optional(bool, false)<br/>    }), {})<br/>  })</pre> | `null` | no |

## Outputs

No outputs.
<!-- END_TF_DOCS -->