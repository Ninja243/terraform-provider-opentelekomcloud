---
subcategory: "Domain Name Service (DNS)"
layout: "opentelekomcloud"
page_title: "OpenTelekomCloud: opentelekomcloud_dns_zone_v2"
sidebar_current: "docs-opentelekomcloud-resource-dns-zone-v2"
description: |-
  Manages a DNS Zones resource within OpenTelekomCloud.
---

Up-to-date reference of API arguments for DNS zones you can get at
[documentation portal (private zone)](https://docs.otc.t-systems.com/domain-name-service/api-ref/apis/private_zone_management) and
[documentation portal (public zone)](https://docs.otc.t-systems.com/domain-name-service/api-ref/apis/public_zone_management)

# opentelekomcloud_dns_zone_v2

Manages a DNS zone in the OpenTelekomCloud DNS Service.

## Example Usage

### Public Zone Configuration

```hcl
resource "opentelekomcloud_dns_zone_v2" "public_example_com" {
  name        = "public.example.com."
  email       = "public@example.com"
  description = "An example for public zone"
  ttl         = 3000
  type        = "public"

  tags = {
    foo = "bar"
    key = "value"
  }
}
```

### Public Zone with DNSSEC

```hcl
resource "opentelekomcloud_dns_zone_v2" "signed_example_com" {
  name   = "signed.example.com."
  email  = "public@example.com"
  type   = "public"
  dnssec = "ENABLE"
}

# Publish this DS record at the domain registrar to complete the chain of trust.
output "ds_record" {
  value = opentelekomcloud_dns_zone_v2.signed_example_com.dnssec_infos[0].ds_record
}
```

### Private Zone Configuration

```hcl
resource "opentelekomcloud_dns_zone_v2" "private_example_com" {
  name        = "private.example.com."
  email       = "private@example.com"
  description = "An example for private zone"
  ttl         = 3000
  type        = "private"

  router {
    router_id     = var.vpc_id
    router_region = var.region
  }
  tags = {
    foo = "bar"
    key = "value"
  }
}
```

### Private Zone Configuration with multiple routers

```hcl
resource "opentelekomcloud_dns_zone_v2" "private_example_com" {
  name        = "private.example.com."
  email       = "private@example.com"
  description = "An example for private zone"
  ttl         = 3000
  type        = "private"

  router {
    router_id     = var.vpc_id_1
    router_region = var.region
  }

  router {
    router_id     = var.vpc_id_2
    router_region = var.region
  }
  tags = {
    foo = "bar"
    key = "value"
  }
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the zone.   Changing this creates a new DNS zone.
-> **Note:** The `.` at the end of the name.

* `email` - (Optional) The email contact for the zone record.

* `type` - (Optional) The type of zone. Can either be `public` or `private`.
  Changing this creates a new zone.

* `ttl` - (Optional) The time to live (TTL) of the zone.

* `description` - (Optional) A description of the zone.

* `router` - (Optional) The Routers(VPCs) configuration for the private zone.
  it is required when type is `private`.

* `tags` - (Optional) The key/value pairs to associate with the zone.

* `value_specs` - (Optional) Map of additional options. Changing this creates a new zone.

* `dnssec` - (Optional) Whether DNSSEC is enabled for a `public` zone. Can either be `ENABLE` or `DISABLE`.
  Enabling signs the zone; the resulting DS record (see `dnssec_infos`) still has to be published at
  the domain registrar to complete the chain of trust. When omitted, the current state of the zone is
  kept as is; removing `dnssec = "ENABLE"` from the configuration does *not* disable DNSSEC.

~> **Warning:** Delete the DS record at the registrar *before* setting `dnssec` to `DISABLE` or
  destroying the zone. An orphaned DS record makes the zone unresolvable on validating resolvers.
  The provider emits a warning whenever it disables DNSSEC or deletes a signed zone.

The `router` block supports:

* `router_id` - (Required) The Router(VPC) ID. which VPC network will assicate with.

* `router_region` - (Required) The Region name for this private zone.

## Attributes Reference

The following attributes are exported:

* `name` - See Argument Reference above.

* `email` - See Argument Reference above.

* `type` - See Argument Reference above.

* `ttl` - See Argument Reference above.

* `description` - See Argument Reference above.

* `tags` - See Argument Reference above.

* `value_specs` - See Argument Reference above.

* `masters` - An array of master DNS servers.

* `dnssec_infos` - DNSSEC key material of a signed public zone. Empty while `dnssec` is `DISABLE`.
  * `key_tag` - Key tag of the KSK.
  * `flag` - DNSKEY flags (`257` for a KSK).
  * `digest_algorithm` - Digest algorithm name (e.g. `SHA256`).
  * `digest_type` - Digest algorithm number (e.g. `2`).
  * `digest` - Digest of the KSK.
  * `signature` - Signing algorithm name (e.g. `ECDSAP256SHA256`).
  * `signature_type` - Signing algorithm number (e.g. `13`).
  * `ksk_public_key` - Base64 public key of the KSK.
  * `ds_record` - Complete DS record to publish at the registrar.
  * `created_at` - Time DNSSEC was enabled.
  * `updated_at` - Time the DNSSEC configuration was last updated.

## Import

This resource can be imported by specifying the zone ID:

```sh
terraform import opentelekomcloud_dns_zone_v2.zone_1 <zone_id>
```
