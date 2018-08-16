# Terminate an application {#reference_jw5_cyw_wdb .reference}

Send signals to all the containers in an application in a reverse order of service dependencies.  The default signal terminates all the containers.

## Request information {#section_nww_2yw_wdb .section}

**Request line \(RequestLine\)**

```
Post/projects/{name}/kill? Signal = {signal} HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name|Type|Required or not|Description|
|:---|:---|:--------------|:----------|
|name|string|Yes|The application name.|
|Signal|String|No|The default value is KILL.|

**Special request header \(RequestHead\)**

N/A

**Request body \(RequestBody\)**

None

## Response information {#section_m1k_yyw_wdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 200 OK
```

**Special response header \(ResponseHead\)**

None

**Response body \(ResponseBody\)**

None

## Example {#section_and_bzw_wdb .section}

**Request example**

```
Post/projects/test/kill HTTP/1.1
```

**Response example**

```
HTTP/1.1 200 OK
```

