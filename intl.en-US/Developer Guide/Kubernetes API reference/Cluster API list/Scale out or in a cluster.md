# Scale out or in a cluster {#reference_agj_fx2_xdb .reference}

Add or remove the worker nodes to or from the cluster \(this operation only applies to the resources created by Resource Orchestration Service \(ROS\)\).

## Request Information {#section_r2n_jx2_xdb .section}

**Request line \(RequestLine\)**

```
PUT /clusters/{cluster_id} HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name |Type|Required or not|Description|
|:----|:---|:--------------|:----------|
|cluster\_id|string|Yes.|The cluster ID.|

**Special request header \(RequestHead\)**

None. Refer to [Public request headers](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Public parameters.md#section_mr5_lf1_wdb).

**Request body requestbody**

```
{
    "disable_rollback": "whether or not to roll back if the cluster fails to be scaled out or in",
    "timeout_mins": "timeout for creating the cluster",
    "worker_instance_type": "instance type of worker nodes",
    "worker_system_disk_category": "system disk type of worker nodes",
    "worker_system_disk_size": "system disk size of worker nodes",
    "login_password": "password used to log on to the node by using SSH",    
    "num_of_nodes": "number of worker nodes"
}
```

**Request body explanation**

|Name |Type|Required|Description|
|:----|:---|:-------|:----------|
|disable\_rollback|bool|Yes|Whether or not to roll back if the cluster fails to be scaled out or in. true indicates to not roll back and false indicates to roll back. Resources generated in the creation process are released if you select to roll back. We do not recommend that you use false.|
|timeout\_mins|int|Yes|The timeout \(in minutes\) for creating the cluster resource stack. The default value is 60.|
|worker\_instance\_type|string|Yes|The Elastic Compute Service \(ECS\) instance type code of the worker nodes. For more details, see [Instance type families](../../../../intl.en-US/Product Introduction/Instance type families.md#).|
|worker\_system\_disk\_category|string|Yes|Worker node System Tray type.|
|worker\_system\_disk\_size|int|Yes|The system disk size of the worker nodes.|
|login\_password|string|Yes|The SSH logon password, which is 8–30 characters long and must contain uppercase letters/lowercase letters, numbers, and special characters at the same time. This password must be the same as that specified when you create the cluster.|
|num\_of\_nodes|int|Yes|The number of worker nodes. The value range is \[0,300\]. To scale out a cluster, this value must be larger than the number of existing worker nodes in the cluster. To scale in a cluster, this value must be smaller than the number of existing worker nodes in the cluster.|

## Returns Information {#section_w1l_gz2_xdb .section}

**Returns the row responseline**

```
HTTP/1.1 202 accepted
```

**Special return head responsehead**

None. See [Public response headers](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Public parameters.md#section_zr5_lf1_wdb).

**Response body \(ResponseBody\)**

```
{
    "cluster_id":"string",
    "request_id":"string",
    "task_id":"string"
}
```

## Examples {#section_dsh_jz2_xdb .section}

**Request example**

```
PUT /clusters/Cccfd68c474454665ace07efce924f75f HTTP/1.1
<Public request headers>
{
    "disable_rollback": true,
    "timeout_mins": 60,
    "num_of_nodes": 8,
    "worker_instance_type": "ecs.sn1ne.large",
    "worker_system_disk_category": "cloud_efficiency",
    "worker_system_disk_size": 40,
    "login_password": "Hello1234"
}
```

**Returns an example**

```
HTTP/1.1 202 Accepted
<Public response headers>
{
    "cluster_id": "Cccfd68c474454665ace07efce924f75f",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

