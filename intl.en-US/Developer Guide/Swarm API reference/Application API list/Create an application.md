# Create an application {#reference_wmk_1wc_wdb .reference}

Create an application.

## Request Information  {#section_ubn_cwc_wdb .section}

**Request line \(RequestLine\)**

```
POST /projects/ HTTP/1.1
```

**Request line parameter \(URI Param\)**

None.

**Special request header \(RequestHead\)**

```
Content-Type: application/json
```

**Request body \(RequestBody\)**

JSON object

```

    "name": "string",
    "description": "string",
    "template": "string",
    "version": "string",
    "environment": {
       "key": "value",
       
    }

```

**Request body explanation**

|Name |Type |Required|Description |
|:----|:----|:-------|:-----------|
|name |string |Yes |The application name,  which can be 1-64 characters long and contain numbers, English letters, and hyphens \(-\), but cannot start with hyphens \(-\).|
|description|string|No |The application description.|
|template |string|Yes|The application Compose yaml template in the format of strings, which must be escaped in the JSON format.|
|version |string |No |The application version. The default value is 1.0.|
|environment |map|No |key/value used to replace the variable parameter in the Compose template.|
|latest\_image|bool |No |Whether or not to update the image before creating an application.|

## Response information {#section_zxz_pwc_wdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 201 Created
```

**Special response header \(ResponseHead\)**

```
Location /projects/<name>
```

## Example  {#section_yp1_bxc_wdb .section}

**Request example **

```
POST /projects HTTP/1.1
Content-Type: application/json

  "name": "test",
  "description": "This is a test application",
  "template": "web:\r\n image: nginx",
  "version": "1.0",
  "environment": {
    "USER": "abc",
    "PWD": "password"
  

```

**Response example **

```
HTTP/1.1 201 Created 
Location /projects/test
```

