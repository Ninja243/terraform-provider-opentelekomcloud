---
subcategory: "Domain Name Service (DNS)"
layout: "opentelekomcloud"
page_title: "OpenTelekomCloud: opentelekomcloud_dns_zone_v2"
sidebar_current: "docs-opentelekomcloud-datasource-dns-zone-v2"
description: |-
  Get available DNS zone from OpenTelekomCloud
---

Up-to-date reference of API arguments for DNS zone you can get at
[documentation portal (private zone)](https://docs.otc.t-systems.com/domain-name-service/api-ref/apis/private_zone_management/querying_private_zones.html#dns-api-63006) and
[documentation portal (public zone)](https://docs.otc.t-systems.com/domain-name-service/api-ref/apis/public_zone_management/querying_public_zones.html#dns-api-62003)

# opentelekomcloud_dns_zone_v2

Use this data source to get the ID of an available OpenTelekomCloud DNS zone.

## Example Usage

```hcl
data "opentelekomcloud_dns_zone_v2" "zone_1" {
  name = "example.com."
}
```

## Argument Reference

* `id` - (Optional) The ID of the zone. If specified, the zone is retrieved
  directly by ID and other lookup arguments are ignored.

* `zone_type` - (Optional) The type of the zone: `private` or `public`.
  This argument is **required** to match `private` zones.

* `name` - (Optional) The name of the zone. A fuzzy search will be performed.

* `description` - (Optional) A description of the zone.

* `email` - (Optional) The email contact for the zone record.

* `status` - (Optional) The zone's status.

* `ttl` - (Optional) The time to live (TTL) of the zone.

* `tags` - (Optional) Tags map to be matched.
  An exact match will be performed. If the value starts with an
  asterisk (*), the string following the asterisk is fuzzy matched.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `id` - The ID of the zone.

* `masters` - An array of master DNS servers.

* `created_at` - The time the zone was created.

* `updated_at` - The time the zone was last updated.

* `serial` - The serial number of the zone.

* `pool_id` - The ID of the pool hosting the zone.

* `project_id` - The project ID that owns the zone.

* `dnssec` - Whether DNSSEC is enabled for a public zone (`ENABLE` or `DISABLE`).

* `dnssec_infos` - DNSSEC key material of a signed public zone. Empty unless `dnssec` is `ENABLE`.
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
