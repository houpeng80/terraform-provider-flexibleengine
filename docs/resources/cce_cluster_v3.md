---
subcategory: "Cloud Container Engine (CCE)"
description: ""
page_title: "flexibleengine_cce_cluster_v3"
---

# flexibleengine_cce_cluster_v3

Provides a cluster resource (CCE).

## Example Usage

 ```hcl
variable "flavor_id" { }

resource "flexibleengine_vpc_v1" "example_vpc" {
  name = "example-vpc"
  cidr = "192.168.0.0/16"
}

resource "flexibleengine_vpc_subnet_v1" "example_subnet" {
  name       = "example-vpc-subnet"
  cidr       = "192.168.0.0/24"
  gateway_ip = "192.168.0.1"
  vpc_id     = flexibleengine_vpc_v1.example_vpc.id
}

resource "flexibleengine_cce_cluster_v3" "cluster_1" {
  name                   = "cluster"
  cluster_type           = "VirtualMachine"
  authentication_mode    = "rbac"
  description            = "new cluster"
  flavor_id              = var.flavor_id
  vpc_id                 = flexibleengine_vpc_v1.example_vpc.id
  subnet_id              = flexibleengine_vpc_subnet_v1.example_subnet.id
  container_network_type = "overlay_l2"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) Cluster name. Changing this parameter will create a new cluster resource.

* `labels` - (Optional) Cluster tag, key/value pair format. Changing this parameter will create a new cluster resource.

* `annotations` - (Optional) Cluster annotation, key/value pair format.
  Changing this parameter will create a new cluster resource.

* `flavor_id` - (Required) Cluster specifications. Changing this parameter will create a new cluster resource.

  + `cce.s1.small` - small-scale single cluster (up to 50 nodes).
  + `cce.s1.medium` - medium-scale single cluster (up to 200 nodes).
  + `cce.s1.large` - large-scale single cluster (up to 1000 nodes).
  + `cce.s2.small` - small-scale HA cluster (up to 50 nodes).
  + `cce.s2.medium` - medium-scale HA cluster (up to 200 nodes).
  + `cce.s2.large` - large-scale HA cluster (up to 1000 nodes).
  + `cce.t1.small` - small-scale single physical machine cluster (up to 10 nodes).
  + `cce.t1.medium` - medium-scale single physical machine cluster (up to 100 nodes).
  + `cce.t1.large` - large-scale single physical machine cluster (up to 500 nodes).
  + `cce.t2.small` - small-scale HA physical machine cluster (up to 10 nodes).
  + `cce.t2.medium` - medium-scale HA physical machine cluster (up to 100 nodes).
  + `cce.t2.large` - large-scale HA physical machine cluster (up to 500 nodes).

* `cluster_version` - (Optional) For the cluster version, possible values are listed on the
  [CCE Cluster Version Release Notes](https://docs.prod-cloud-ocb.orange-business.com/usermanual2/cce/cce_01_0068.html).
  If this parameter is not set, the latest available version will be used.

* `cluster_type` - (Required) Cluster Type, possible values are VirtualMachine and BareMetal.
  Changing this parameter will create a new cluster resource.

* `description` - (Optional) Cluster description.

* `billing_mode` - (Optional) Charging mode of the cluster, which is 0 (on demand).
  Changing this parameter will create a new cluster resource.

* `extend_param` - (Optional) Extended parameter. Changing this parameter will create a new cluster resource.

* `vpc_id` - (Required) The ID of the VPC used to create the node. Changing this parameter will create a new cluster resource.

* `subnet_id` - (Required) The ID of the VPC Subnet used to create the node.
  Changing this parameter will create a new cluster resource.

* `highway_subnet_id` - (Optional) The ID of the high speed network used to create bare metal nodes.
    Changing this parameter will create a new cluster resource.

* `container_network_type` - (Required) Container network parameters. Possible values:

  + `overlay_l2` - An overlay_l2 network built for containers by using Open vSwitch(OVS)
  + `underlay_ipvlan` - An underlay_ipvlan network built for bare metal servers by using ipvlan.
  + `vpc-router` - An vpc-router network built for containers by using ipvlan and custom VPC routes.

* `container_network_cidr` - (Optional) Container network segment. Changing this parameter will create a new cluster resource.

* `service_network_cidr` - (Optional) Service network segment. Changing this parameter will create a new cluster resource.

* `authentication_mode` - (Optional) Authentication mode of the cluster, possible values are x509 and rbac. Defaults to *rbac*.
    Changing this parameter will create a new cluster resource.

* `authenticating_proxy_ca` - (Optional) CA root certificate provided in the authenticating_proxy mode. The CA root certificate
    is encoded to the Base64 format. Changing this parameter will create a new cluster resource.

* `eip` - (Optional) EIP address of the cluster.

* `kube_proxy_mode` - (Optional, String, ForceNew) Service forwarding mode. Two modes are available:

  + iptables: Traditional kube-proxy uses iptables rules to implement service load balancing.
    In this mode, too many iptables rules will be generated when many services are deployed. In addition,
    non-incremental updates will cause a latency and even obvious performance issues in the case of heavy service traffic.
  + ipvs: Optimized kube-proxy mode with higher throughput and faster speed. This mode supports incremental updates and
    can keep connections uninterrupted during service updates. It is suitable for large-sized clusters.

* `masters` - (Optional, List, ForceNew) Advanced configuration of master nodes. Changing this creates a new cluster.

The `masters` block supports:

* `availability_zone` - (Optional, String, ForceNew) Specifies the availability zone of the master node.
  Changing this creates a new cluster.

## Attributes Reference

All above argument parameters can be exported as attribute parameters along with attribute reference.

* `id` -  Id of the cluster resource.

* `status` -  Cluster status information.

* `internal_endpoint` - The internal network address.

* `external_endpoint` - The external network address.

* `external_apig_endpoint` - The endpoint of the cluster to be accessed through API Gateway.

* `security_group_id` - Security group ID of the cluster.

* `certificate_clusters.name` - The cluster name.

* `certificate_clusters.server` - The server IP address.

* `certificate_clusters.certificate_authority_data` - The certificate data.

* `certificate_users.name` - The user name.

* `certificate_users.client_certificate_data` - The client certificate data.

* `certificate_users.client_key_data` - The client key data.

## Import

Cluster can be imported using their `id`, e.g.

```shell
terraform import flexibleengine_cce_cluster_v3.cluster_1 4779ab1c-7c1a-44b1-a02e-93dfc361b32d  
```
