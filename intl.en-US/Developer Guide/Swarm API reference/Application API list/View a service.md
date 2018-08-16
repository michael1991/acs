# View a service {#reference_nhp_dpy_wdb .reference}

View the service details according to the service ID.

## Request information {#section_mz2_gpy_wdb .section}

**Request line \(RequestLine\)**

```
GET /services/{service_id} HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name |Type |Required |Description |
|:----|:----|:--------|:-----------|
|service\_id|string |Yes |The service ID in the format of \{project\_name\}\_\{service\_name\}.|

**Special request header \(RequestHead\)**

None.

**Request body \(RequestBody\)**

None.

## Response information {#section_h3b_4py_wdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 200 OK
```

**Special response header \(ResponseHead\)**

None.

**Response body \(ResponseBody\)**

```

    "id": "string",
    "name": "string",
    "project": "string",
    "description": "string",
    "created": "datetime",
    "updated": "datetime",
    "desired_state": "string",
    "current_state": "string",
    "definition": {
       "key": "value",
       
    
    "extensions": {
       "key": "value",
       
    
    "containers": {
       "key": "value",
       
    }

```

**Response body explanation**

Service format

|Name |Type |Description |
|:----|:----|:-----------|
|id|string|The service ID. |
|name |string |The service name.|
|project |string|The application name.|
|created|datetime |The created time of the service.|
|updated|datetime|The updated time of the service.|
|desired\_state|string|The expected status \(if the current status is the intermediate status, the expected status indicates the final status of the change\).|
|current\_state|string|The current status.|
|definition|map|The service definition key/value in Compose.|
|extensions|map|The service extension key/value in the Container Service Compose.|
|containers|map|The container key \(container ID\)/value \(attribute\) in the service.|

## Example  {#section_oxp_vpy_wdb .section}

**Request example **

```
GET /services/wordpress_db HTTP/1.1
```

**Response example **

```
HTTP/1.1 200 Ok
Content-Type:application/json;charset=UTF-8

  "id": "wordpress_db",
  "name": "db",
  "project": "wordpress",
  "definition": {
    "environment": [
      "MYSQL_ROOT_PASSWORD=password"
    
    "image": "mysql:5.7",
    "restart": "always"
  
  "extensions": {
    "scale": 1,
    "logs": [
      "/var/log/mysql"
    
  
  "created": "2016-04-21T13:36:32.440646459Z",
  "updated": "2016-04-21T13:36:33.270308958Z",
  "desired_state": "running",
  "current_state": "running",
  "containers": {
    "5616f05d27516b3502a391fd2ca9d312cabffa5ad431bf261ea81f4ceabd476e": {
      "name": "/wordpress_db_1",
      "node": "10.246.2.3",
      "ip": "10.0.0.2",
      "running": true,
      "status": "running",
      "health": "success"
    
  

```

