# View a cluster {#reference_a2f_pvb_wdb .reference}

View the cluster details according to the cluster ID. For more information about the API description, see [Container Service API reference](intl.en-US/Developer Guide/Swarm API reference/Cluster API list/View a cluster.md#).

## Scope {#section_gf2_vvb_wdb .section}

Swarm clusters and Kubernetes clusters.

## API request and response {#section_yhs_vvb_wdb .section}

**Request format**

```
aliyun cs GET /clusters/<cluster_id>
```

**Response results**

```
{
    "Agent_version": "string ",
    "cluster_id": "string",
    "created": "datetime",
    "external_loadbalancer_id": "string",
    "master_url": "string",
    "name": "string",
    "network_mode": "string",
    "region_id": "string",
    "security_group_id": "string",
    "size": "numbers",
    "state": "string",
    "updated": "datetime",
    "vpc_id": "string",
    "vswitch_id": "string"
}
```

