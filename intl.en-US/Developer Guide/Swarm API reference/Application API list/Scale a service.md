# Scale a service {#reference_hhk_jty_wdb .reference}

Scale the containers in a service to a specific number.

## Request information  {#section_oqd_lty_wdb .section}

**Request line \(RequestLine\)**

```
POST /services/{service_id}/scale HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name |Type |Required|Description |
|:----|:----|:-------|:-----------|
|service\_id|string |Yes |The service ID in the format of \{project\_name\}\_\{service\_name\}. |

**Special request header \(RequestHead\)**

None.

**Request body \(RequestBody\)**

JSON object

```

    "type": "string",
    "value": "int"

```

## Response information {#section_hn5_sty_wdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 200 OK
```

**Special response header \(ResponseHead\)**

None.

**Response body \(ResponseBody\)**

None.

## Example  {#section_njd_vty_wdb .section}

**Request example **

Change the number of containers in `test_web` service to three.

```
POST /services/test_web/scale HTTP/1.1
Content-Type: application/json

    "type": "scale_to",
    "value": 3

```

**Response example **

```
HTTP/1.1 200 OK
```

