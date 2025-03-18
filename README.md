# VPN Module

A module designed to create a vpn tunnel.

Usage example:

```hcl
module "vpc" {
  source = "iits-consulting/vpc/opentelekomcloud"
  name   = "${var.context}-${var.stage}-vpc"

  cidr_block = var.vpc_cidr
  subnets    = {
    "vpn-subnet" = "175.1.2.0/24"
  }
  tags       = local.tags
}

resource "random_password" "vpn_psk" {
  length      = 32
  special     = false
  min_lower   = 1
  min_numeric = 1
  min_upper   = 1
}

module "vpn_tunnel" {
  source = "iits-consulting/vpn/opentelekomcloud"
  name   = "${var.context}-${var.stage}-vpn"

  psk            = random_password.vpn_psk.result
  dpd            = var.vpn_dpd
  remote_gateway = "180.12.12.0"
  remote_subnets = ["170.10.1.0/24"]
  local_router   = module.vpc.vpc.id
  local_subnets  = values(module.vpc.subnets).*.cidr

  vpn_ike_policy_dh_algorithm         = var.vpn_ike_policy_dh_algorithm
  vpn_ike_policy_auth_algorithm       = var.vpn_ike_policy_auth_algorithm
  vpn_ike_policy_encryption_algorithm = var.vpn_ike_policy_encryption_algorithm
  vpn_ike_policy_lifetime             = var.vpn_ike_policy_lifetime

  vpn_ipsec_policy_protocol             = var.vpn_ipsec_policy_protocol
  vpn_ipsec_policy_auth_algorithm       = var.vpn_ipsec_policy_auth_algorithm
  vpn_ipsec_policy_encryption_algorithm = var.vpn_ipsec_policy_encryption_algorithm
  vpn_ipsec_policy_lifetime             = var.vpn_ipsec_policy_lifetime
  vpn_ipsec_policy_pfs                  = var.vpn_ipsec_policy_pfs

  tags = local.tags
}
```

Notes:

- example of a full vpn tunnel to be found in the [Terratest section](https://github.com/iits-consulting/terraform-opentelekomcloud-project-factory/tree/master/terratest/vpn)

<!-- BEGIN_TF_DOCS -->

## Requirements

| Name                                                                                          | Version  |
| --------------------------------------------------------------------------------------------- | -------- |
| <a name="requirement_opentelekomcloud"></a> [opentelekomcloud](#requirement_opentelekomcloud) | >=1.34.1 |

## Providers

| Name                                                                                    | Version  |
| --------------------------------------------------------------------------------------- | -------- |
| <a name="provider_opentelekomcloud"></a> [opentelekomcloud](#provider_opentelekomcloud) | >=1.34.1 |

## Modules

No modules.

## Resources

| Name                                                                                                                                                                                      | Type     |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| [opentelekomcloud_vpnaas_endpoint_group_v2.local_peer](https://registry.terraform.io/providers/opentelekomcloud/opentelekomcloud/latest/docs/resources/vpnaas_endpoint_group_v2)          | resource |
| [opentelekomcloud_vpnaas_endpoint_group_v2.remote_peer](https://registry.terraform.io/providers/opentelekomcloud/opentelekomcloud/latest/docs/resources/vpnaas_endpoint_group_v2)         | resource |
| [opentelekomcloud_vpnaas_ike_policy_v2.ike_policy](https://registry.terraform.io/providers/opentelekomcloud/opentelekomcloud/latest/docs/resources/vpnaas_ike_policy_v2)                  | resource |
| [opentelekomcloud_vpnaas_ipsec_policy_v2.ipsec_policy](https://registry.terraform.io/providers/opentelekomcloud/opentelekomcloud/latest/docs/resources/vpnaas_ipsec_policy_v2)            | resource |
| [opentelekomcloud_vpnaas_service_v2.vpnaas_service](https://registry.terraform.io/providers/opentelekomcloud/opentelekomcloud/latest/docs/resources/vpnaas_service_v2)                    | resource |
| [opentelekomcloud_vpnaas_site_connection_v2.tunnel_connection](https://registry.terraform.io/providers/opentelekomcloud/opentelekomcloud/latest/docs/resources/vpnaas_site_connection_v2) | resource |

## Inputs

| Name                                                                                                                                             | Description                                                                 | Type          | Default | Required |
| ------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- | ------------- | ------- | :------: |
| <a name="input_local_router"></a> [local_router](#input_local_router)                                                                            | VPC id of the vpnaas service.                                               | `string`      | n/a     |   yes    |
| <a name="input_local_subnets"></a> [local_subnets](#input_local_subnets)                                                                         | Local subnet CIDR ranges.                                                   | `set(string)` | n/a     |   yes    |
| <a name="input_name"></a> [name](#input_name)                                                                                                    | Prefix for all OTC resource names                                           | `string`      | n/a     |   yes    |
| <a name="input_psk"></a> [psk](#input_psk)                                                                                                       | Pre shared key for vpn tunnel.                                              | `string`      | n/a     |   yes    |
| <a name="input_remote_gateway"></a> [remote_gateway](#input_remote_gateway)                                                                      | Remote endpoint IPv4 address.                                               | `string`      | n/a     |   yes    |
| <a name="input_remote_subnets"></a> [remote_subnets](#input_remote_subnets)                                                                      | Remote subnet CIDR ranges.                                                  | `set(string)` | n/a     |   yes    |
| <a name="input_tags"></a> [tags](#input_tags)                                                                                                    | Common tag set for project resources                                        | `map(string)` | n/a     |   yes    |
| <a name="input_vpn_ike_policy_auth_algorithm"></a> [vpn_ike_policy_auth_algorithm](#input_vpn_ike_policy_auth_algorithm)                         | Authentication hash algorithm                                               | `string`      | n/a     |   yes    |
| <a name="input_vpn_ike_policy_dh_algorithm"></a> [vpn_ike_policy_dh_algorithm](#input_vpn_ike_policy_dh_algorithm)                               | Diffie-Hellman key exchange algorithm                                       | `string`      | n/a     |   yes    |
| <a name="input_vpn_ike_policy_encryption_algorithm"></a> [vpn_ike_policy_encryption_algorithm](#input_vpn_ike_policy_encryption_algorithm)       | Encryption algorithm                                                        | `string`      | n/a     |   yes    |
| <a name="input_vpn_ipsec_policy_auth_algorithm"></a> [vpn_ipsec_policy_auth_algorithm](#input_vpn_ipsec_policy_auth_algorithm)                   | Authentication hash algorithm                                               | `string`      | n/a     |   yes    |
| <a name="input_vpn_ipsec_policy_encryption_algorithm"></a> [vpn_ipsec_policy_encryption_algorithm](#input_vpn_ipsec_policy_encryption_algorithm) | Encryption algorithm                                                        | `string`      | n/a     |   yes    |
| <a name="input_vpn_ipsec_policy_pfs"></a> [vpn_ipsec_policy_pfs](#input_vpn_ipsec_policy_pfs)                                                    | The perfect forward secrecy mode                                            | `string`      | n/a     |   yes    |
| <a name="input_vpn_ipsec_policy_protocol"></a> [vpn_ipsec_policy_protocol](#input_vpn_ipsec_policy_protocol)                                     | The security protocol used for IPSec to transmit and encapsulate user data. | `string`      | n/a     |   yes    |
| <a name="input_dpd"></a> [dpd](#input_dpd)                                                                                                       | Dead peer detection (true = hold (default) false = disabled).               | `bool`        | `true`  |    no    |
| <a name="input_vpn_ike_policy_lifetime"></a> [vpn_ike_policy_lifetime](#input_vpn_ike_policy_lifetime)                                           | Lifetime of the security association in seconds.                            | `number`      | `86400` |    no    |
| <a name="input_vpn_ipsec_policy_lifetime"></a> [vpn_ipsec_policy_lifetime](#input_vpn_ipsec_policy_lifetime)                                     | Lifetime of the security association in seconds.                            | `number`      | `3600`  |    no    |

## Outputs

| Name                                                                                      | Description |
| ----------------------------------------------------------------------------------------- | ----------- |
| <a name="output_vpn_tunnel_gateway"></a> [vpn_tunnel_gateway](#output_vpn_tunnel_gateway) | n/a         |

<!-- END_TF_DOCS -->
