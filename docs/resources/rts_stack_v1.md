---
subcategory: "Resource Template Service (RTS)"
description: ""
page_title: "flexibleengine_rts_stack_v1"
---

# flexibleengine_rts_stack_v1

Provides a FlexibleEngine Stack.

## Example Usage

 ```hcl
 variable "name" { }
 variable "instance_type" { }
 variable "image_id" { }
 
resource "flexibleengine_rts_stack_v1" "mystack" {
  name             = var.name
  disable_rollback = true
  timeout_mins     =60
  parameters = {
      "network_id"    = flexibleengine_vpc_subnet_v1.example_subnet.id
      "instance_type" = var.instance_type
      "image_id"      = var.image_id
    }
  template_body = <<STACK
  {
    "heat_template_version": "2016-04-08",
    "description": "Simple template to deploy",
    "parameters": {
        "image_id": {
            "type": "string",
            "description": "Image to be used for compute instance",
            "label": "Image ID"
        },
        "network_id": {
            "type": "string",
            "description": "The Network to be used",
            "label": "Network UUID"
        },
        "instance_type": {
            "type": "string",
            "description": "Type of instance (Flavor) to be used",
            "label": "Instance Type"
        }
    },
    "resources": {
        "my_instance": {
            "type": "OS::Nova::Server",
            "properties": {
                "image": {
                    "get_param": "image_id"
                },
                "flavor": {
                    "get_param": "instance_type"
                },
                "networks": [{
                    "network": {
                        "get_param": "network_id"
                    }
                }]
            }
        }
    },
    "outputs":  {
      "InstanceIP":{
        "description": "Instance IP",
        "value": {  "get_attr": ["my_instance", "first_address"]  }
      }
    }
}
STACK
 }
 ```

## Argument Reference

The following arguments are supported:

* `name` - (Required) A unique name for the stack. The value must meet the regular expression rule (`^[a-zA-Z][a-zA-Z0-9_.-]{0,254}$`).
  Changing this creates a new stack.

* `template_body` - (Optional; Required if `template_url` is empty) Structure containing the template body.
  The template content must use the yaml syntax.

* `template_url` - (Optional; Required if `template_body` is empty) Location of a file containing the template body.

* `environment` - (Optional) Tthe environment information about the stack.

* `files` - (Optional) Files used in the environment.

* `parameters` - (Optional) A list of Parameter structures that specify input parameters for the stack.

* `disable_rollback` - (Optional) Set to true to disable rollback of the stack if stack creation failed.

* `timeout_mins` - (Optional) Specifies the timeout duration.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `outputs` - A map of outputs from the stack.

* `capabilities` - List of stack capabilities for stack.

* `notification_topics` - List of notification topics for stack.

* `status` - Specifies the stack status.

## Import

RTS Stacks can be imported using the `name`, e.g.

```shell
terraform import flexibleengine_rts_stack_v1.mystack rts-stack
```

## Timeouts

`flexibleengine_rts_stack_v1` provides the following
[Timeouts](/docs/configuration/resources.html#timeouts) configuration options:

* `create` - (Default `30 minutes`) Used for Creating Stacks
* `update` - (Default `30 minutes`) Used for Stack modifications
* `delete` - (Default `30 minutes`) Used for destroying stacks.
