# Create multi-availability kubernetes Cluster {#reference_yz2_qnk_xdb .reference}

Creates a new high-availability kubernetes cluster instance across the availability area, and creates a new specified number of nodes.

## Request Information {#section_gm1_wnk_xdb .section}

**Request Line requestline**

```
POST /clusters HTTP/1.1
```

**Special request header requesthead**

None. Refer to [Public request headers](intl.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_cdv_4nd_xdb).

**Request body \(RequestBody\)**

```
{
    "Maid": "whether the failure rolls back ",
    "Name": "cluster name ",
    "Maid": cluster creation timeout,
    "Cluster_type": "kubernetes ",
    "Region_id": "region"
    "Multi_az": True,
    "Vpcid": "vpc id ",
    "Maid": "CIDR container ",         
    "Service_cidr": "service CIDR ",
    "Maid": "First available zone switch ID ",
    "Maid": "second usable zone switch ID ",
    "Maid": "third usable zone switch ID ",        
    "Maid": "First available Zone master node instance specification ",
    "Maid": "second available area master node instance specification ",
    "Maid": "third available area master node instance specification ", 
    "Maid": "master node System Disk Type ",
    "Maid": "master node System Disk size ",               
    "Maid": "First available zone worker node instance specification ",
    "Maid": "second available area worker node instance specification ",
    "Maid": "third available area worker node instance specification ",
    "Maid": "worker node System Tray type ",
    "Maid": "worker node System Disk size ",
    "Maid: the number of worker nodes in the first available area ",
    MAID: the number of worker nodes in the second available area ",
    MAID: the number of worker nodes in the third available area ",
    "Maid": "whether or not to open public-network SSH Login ",
    "Login_password": "SSH Login Password", "maid ": "Whether the cloud monitor plug-in is installed"
}
```

**Request body explanation**

|Name |Parameters|Required|Description|
|:----|:---------|:-------|:----------|
|Disable\_rollback|bool|No |Whether the failure is rolled back or not, true to indicate that the failure is not rolled back, false Failed rollback. If the selection fails to roll back, the resources produced during the creation are released, and false is not recommended|
|Name|string|Yes|Cluster name. The cluster name can be in case English letters, Chinese, numbers, and dashes.|
|Timeout\_mins|integer|No|The cluster resource stack creation time-out, in minutes, is the default of 60.|
|Region\_id|string|Yes|Location ID of the Cluster|
|Multi\_az|bool|Yes|Highly Available Cluster type, fixed to true|
|Vpcid|string|Yes|Vpcid|
|Container\_cidr|string|No |Container segment, cannot conflict with VPC segment. When you select a system to automatically create a VPC, by default, use the LDO .0.0/16 network segment|
|Service\_cidr|string|No |A service segment cannot conflict with a VPC segment and a container segment. When you select a system to automatically create a VPC, the default is to use the FIG/20 network segment|
|Vswitch\_id\_a|string|Yes|Switch ID of the first usable Zone|
|Vswitch\_id\_ B|string|Yes|Switch ID of the second usable Zone|
|Vswitch\_id\_c|string|Yes|Switch ID of the third usable area|
|Master\_instance\_type\_a|string|Yes|The first available Zone master node ECs specification Type code. For more information, see [Instance type families](../../../../intl.en-US/Product Introduction/Instance type families.md#).|
|Master\_instance\_type\_ B|string|Yes|The second usable Zone master node ECs specification Type code. For more information, see [Instance type families](../../../../intl.en-US/Product Introduction/Instance type families.md#).|
|Master\_instance\_type\_c|string|Yes|The third available Zone master node ECs specification Type code. For more information, see [Instance type families](../../../../intl.en-US/Product Introduction/Instance type families.md#).|
|Master\_system\_disk\_category|string|Yes|Master node system disk type.|
|Master\_system\_disk\_size|integer|No|Master node System Disk size|
|Worker\_instance\_type\_a|string|Yes|The first available zone worker node ECs specification Type code. For more details, see.|
|Worker\_instance\_type\_ B|string|Yes|The first available zone worker node ECs specification Type code. For more details, see.|
|Worker\_instance\_type\_c|string|Yes|The first available zone worker node ECs specification Type code. For more details, see.|
|Worker\_system\_disk\_category|string|Yes|Worker node System Tray type.|
|Worker\_system\_disk\_size|integer|No|Worker node System Disk size|
|Num\_of\_nodes\_a|integer|No|The first number of free zone worker nodes. Scope is \[1,300\]|
|Num\_of\_nodes\_ B|integer|No|The first number of free zone worker nodes. Scope is \[1,300\]|
|Num\_of\_nodes\_c|integer|No|The first number of free zone worker nodes. Scope is \[1,300\]|
|Login\_password|string|Yes|SSH Login Password. Password rule is 8-30 Two characters, including three \(large, lower-case letters, numbers, and special symbols \). Key\_pair Two, one.|
|Prosupport pair|string|Yes|The keypair name. And login\_password.|
|Ssh\_flags|bool|No |Do you want to open public network SSH login Login?|
|Cloud\_monitor\_flags|bool|No |Do you want to install the cloud monitoring plug-in?|

**API Usage Note**

-   You first need to include at least 3 switches under the unified VPC, in order to ensure high availability, it is recommended that three switches be distributed in different available zones.
-   The switch to be used by the cluster must have access capability \(SNAT rules can be configured using the NAT Gateway\) or use ECs as the network agent. It is recommended that you configure SNAT rules using a NAT Gateway\)

## Returns Information {#section_zbx_3pk_xdb .section}

**Returns the row responseline**

```
HTTP/1.1 202 accepted
```

**Special return head responsehead**

None. See [Public response headers](intl.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_qdv_4nd_xdb).

**Response body \(ResponseBody\)**

```
{
    "cluster_id":"string",
    "request_id":"string",
    "task_id":"string"
}
```

## Example {#section_gdx_lpk_xdb .section}

**Request example**

```
POST /clusters HTTP/1.1
<Public request header>
{
    "Maid": True,
    "Name": "mulit-Az-cluster ",
    "Maid": 60,
    "Cluster_type": "kubernetes ",
    "Region_id": "CN-Shanghai ",
    "Multi_az": True,
    "Maid": "10.4.0.0/16 ",
    "service_cidr": "10.3.0.0/20",
    "vpcid": "vpc-mytestvpc",
    "vswitch_id_a": "vsw-a",
    "vswitch_id_b": "vsw-b",
    "vswitch_id_c": "vsw-c",
    "master_instance_type_a": "ecs.c5.large",
    "master_instance_type_b": "ecs.d1.2xlarge",
    "master_instance_type_c": "ecs.c4.xlarge",
    "master_system_disk_category": "cloud_efficiency",
    "master_system_disk_size": 40,
    "worker_instance_type_a": "ecs.c5.large",
    "worker_instance_type_b": "ecs.d1.2xlarge",
    "worker_instance_type_c": "ecs.c4.xlarge",
    "worker_system_disk_category": "cloud_efficiency",
    "Maid": 40,
    "num_of_nodes_a": 2,
    "num_of_nodes_b": 2,
    "num_of_nodes_c": 2,
    "ssh_flags": true,
    "login_password": "Hello1234",
    "cloud_monitor_flags": true
}
```

**Returns an example**

```
HTTP/1.1 202 Accepted
<Public response headers>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650d7ce",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

