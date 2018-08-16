# Remove nodes from clusters {#reference_usg_wjd_xdb .reference}

Remove nodes from container clusters according to the cluster ID and node IP address.

## Request information {#section_u4r_xjd_xdb .section}

**Request line \(RequestLine\)**

```
DELETE /clusters/{cluster_id}/ip/{ip}? releaseInstance=true|false HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
|cluster\_id|string|Yes|The cluster ID.|
|ip|string|Yes|The node IP address. The public IP for classic network, and intranet IP for Virtual Private Cloud \(VPC\).|
|releaseInstance|bool|No|Remove nodes and release Elastic Compute Service \(ECS\) instances \(only Pay-As-You-Go ECS instances can be released\).|

**Special request header \(RequestHead\)**

None. See [Public request headers](intl.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_cdv_4nd_xdb).

**Request body \(RequestBody\)**

None. 

## Response information {#section_kyt_2kd_xdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 202 Accepted
```

**Special response header \(ResponseHead\)**

None. See [Public response headers](intl.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_qdv_4nd_xdb).

**Response body \(ResponseBody\)**

None. 

## Example  {#section_et2_hkd_xdb .section}

**Request example**

```
DELETE /clusters/Cccfd68c474454665ace07efce924f75f/ip/10.1.0.123? releaseInstance=false HTTP/1.1
<Public request headers>
```

**Response example**

```
HTTP/1.1 202 Accepted
<Public response headers>
```

