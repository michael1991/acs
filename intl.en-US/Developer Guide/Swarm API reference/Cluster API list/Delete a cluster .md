# Delete a cluster  {#reference_ggy_42d_xdb .reference}

Delete a cluster based on the cluster ID, and release all node resources of the cluster.

## Request information {#section_xsd_q2d_xdb .section}

**Request line \(RequestLine\)**

```
DELETE /clusters/{cluster_id} HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
|cluster\_id|string|Yes|Cluster ID|

**Special request header \(RequestHead\)**

None. See [Public request headers](intl.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_cdv_4nd_xdb).

**Request body \(RequestBody\)**

None. 

## Response information {#section_ynk_x2d_xdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 202 Accepted
```

**Special response header \(ResponseHead\)**

None. See [Public response headers](intl.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_qdv_4nd_xdb).

**Response body \(ResponseBody\)**

None. 

## Example  {#section_x2t_z2d_xdb .section}

**Request example**

```
DELETE /clusters/Cccfd68c474454665ace07efce924f75f HTTP/1.1
<Public request headers>
```

**Response example**

```
HTTP/1.1 202 Accepted
<Public response headers>
```

