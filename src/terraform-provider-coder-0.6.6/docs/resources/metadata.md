---
# generated by https://github.com/hashicorp/terraform-plugin-docs
page_title: "coder_metadata Resource - terraform-provider-coder"
subcategory: ""
description: |-
  Use this resource to attach metadata to a resource. They will be displayed in the Coder dashboard.
---

# coder_metadata (Resource)

Use this resource to attach metadata to a resource. They will be displayed in the Coder dashboard.

## Example Usage

```terraform
data "coder_workspace" "me" {
}

resource "kubernetes_pod" "dev" {
  count = data.coder_workspace.me.start_count
}

resource "tls_private_key" "example_key_pair" {
  algorithm   = "ECDSA"
  ecdsa_curve = "P256"
}

resource "coder_metadata" "pod_info" {
  count       = data.coder_workspace.me.start_count
  resource_id = kubernetes_pod.dev[0].id
  # (Enterprise-only) this resource consumes 200 quota units
  daily_cost = 200
  item {
    key   = "description"
    value = "This description will show up in the Coder dashboard."
  }
  item {
    key   = "pod_uid"
    value = kubernetes_pod.dev[0].uid
  }
  item {
    key   = "public_key"
    value = tls_private_key.example_key_pair.public_key_openssh
    # The value of this item will be hidden from view by default
    sensitive = true
  }
}
```

<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `resource_id` (String) The "id" property of another resource that metadata should be attached to.

### Optional

- `daily_cost` (Number) (Enterprise) The cost of this resource every 24 hours. Use the smallest denomination of your preferred currency. For example, if you work in USD, use cents.
- `hide` (Boolean) Hide the resource from the UI.
- `icon` (String) A URL to an icon that will display in the dashboard. View built-in icons here: https://github.com/coder/coder/tree/main/site/static/icon. Use a built-in icon with `data.coder_workspace.me.access_url + "/icon/<path>"`.
- `item` (Block List) Each "item" block defines a single metadata item consisting of a key/value pair. (see [below for nested schema](#nestedblock--item))

### Read-Only

- `id` (String) The ID of this resource.

<a id="nestedblock--item"></a>
### Nested Schema for `item`

Required:

- `key` (String) The key of this metadata item.

Optional:

- `sensitive` (Boolean) Set to "true" to for items such as API keys whose values should be hidden from view by default. Note that this does not prevent metadata from being retrieved using the API, so it is not suitable for secrets that should not be exposed to workspace users.
- `value` (String) The value of this metadata item.

Read-Only:

- `is_null` (Boolean)

