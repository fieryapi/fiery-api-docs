# Fiery API Documentation

**Base URL:** `https://your-fiery-server`  

---

## Table of Contents

1. [Authentication](#authentication)
   - [POST /live/api/v5/login](#post-liveapiv5login)
   - [POST /live/api/v5/logout](#post-liveapiv5logout)
2. [Status](#status)
   - [GET /live/api/v5/status](#get-liveapiv5status)
3. [General Information](#general-information)
   - [GET /live/api/v5/info](#get-liveapiv5info)
4. [Jobs](#jobs)
   - [POST /live/api/v5/jobs](#post-liveapiv5jobs)
   - [GET /live/api/v5/jobs/:id](#get-liveapiv5jobsid)
5. [Accounting](#accounting)
   - [GET /live/api/v5/accounting](#get-liveapiv5accounting)
   - [GET /live/api/v5/accounting/:id](#get-liveapiv5accountingid)
6. [Licenses](#licenses)
   - [GET /live/api/v5/licenses](#get-liveapiv5licenses)
   - [POST /live/api/v5/licenses](#post-liveapiv5licenses)
   - [GET /live/api/v5/licenses/:id](#get-liveapiv5licensesid)
   - [DELETE /live/api/v5/licenses/:id](#delete-liveapiv5licensesid)
   - [POST /live/api/v5/licenses/requestfiles](#post-liveapiv5licensesrequestfiles)
7. [Consumables](#consumables)
   - [GET /live/api/v5/consumables](#get-liveapiv5consumables)
8. [Devices](#devices)
   - [GET /live/api/v5/devices(/:id)](#get-liveapiv5devicesid)
9. [Presets](#presets)
   - [GET /live/api/v5/presets](#get-liveapiv5presets)
   - [GET /live/api/v5/presets/:id](#get-liveapiv5presetsid)
10. [Queues](#queues)
    - [GET /live/api/v5/queues](#get-liveapiv5queues)
    - [GET /live/api/v5/queues/:id](#get-liveapiv5queuesid)
11. [Properties](#properties)
    - [GET /live/api/v5/properties](#get-liveapiv5properties)
    - [GET /live/api/v5/properties/:id](#get-liveapiv5propertiesid)
12. [Paper Catalog](#paper-catalog)
    - [GET /live/api/v5/papercatalog](#get-liveapiv5papercatalog)
    - [GET /live/api/v5/papercatalog/:id](#get-liveapiv5papercatalogid)

---

## Authentication

### POST /live/api/v5/login

Establishes authorized user access to the Fiery API features.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `apikey` | string | body | Yes | Api Key |
| `username` | string | body | Yes | Your Fiery user name (not guest) |
| `password` | string | body | Yes | Your Fiery password |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Success — `authenticated=true` means successfully logged in, otherwise check username/password. `accessrights=true` means an access key was provided. e.g. `{"authenticated":true or false,"accessrights":"no or yes"}` |
| 400 | The service does not understand the request content. |
| 401 | Access denied when the password is wrong. e.g. `{"authenticated":false, "error_code":[false, {"HARMONY_RESULT_ACCESS_DENIED":-2}]}` |
| 403 | The client is not allowed to access the requested API. |

---

### POST /live/api/v5/logout

Terminates an authorized session initialized by the "login" request.

**Parameters**

_None_

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Successfully logged out. `{"authenticated":false}` — the session is no longer authenticated. |
| 403 | The client is not allowed to access the requested API. |

---

## Status

### GET /live/api/v5/status

Reports a string value indicating the current status of the Fiery software.

**Parameters**

_None_

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Returns a hash. e.g. `{:fiery => "running", "stopped", or "started"}` |
| 403 | The client is not allowed to access the requested API. |

---

## General Information

### GET /live/api/v5/info

Returns Fiery's basic info, such as `name`, `version`, `disk_available`, etc.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `key[]` | string | query | No | A key or an array of keys to filter. e.g. `info?key[]=name&key[]=version` returns values of `name` and `version`. If not specified, returns all keys. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Success — returns a Hash of requested info key names and their corresponding values. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |

---

## Jobs

### POST /live/api/v5/jobs

Upload a job to Fiery.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `file` | binary | query | Yes | Binary content of the file. |
| `queue` | string | query | No | Printer queue id to apply. Preset and user-created queue cannot be used together. |
| `attributes` | string | query | No | Additional attributes to apply. |
| `preset` | string | query | No | ID of the Preset to apply to the job. |
| `pcmid` | string | query | No | ID of the Papercatalog to apply to the job. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Newly created job's ID. |
| 400 | The service does not understand the content of the request. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |

---

### GET /live/api/v5/jobs/:id

Returns job attributes of a job specified by id.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `:id` | string | path | Yes | ID of the job to retrieve. Available job IDs can be retrieved via `GET /live/api/v*/jobs?key[]=id`. |
| `key[]` | string | query | No | One key or array of keys to pull. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Attributes of the Job object including `[id, title, username, status, ...]` |
| 401 | The client is not authorized to access the requested resource. |
| 403 | The client is not allowed to access the requested resource. |
| 404 | The specified resource is not found. |

---

## Accounting

### GET /live/api/v5/accounting

Lists data from the Fiery's job log, containing selected accounting information for each printed job.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `key[]` | string | query | No | An array of selected keys. |
| `filter` | string | query | No | Filter parameters as key-value pairs. e.g. `username=Administrator&title=TestingPrint` |
| `start_time` | integer | query | No | Time range relative to present time. `-60` means last minute. `-3600` means last hour. |
| `end_time` | integer | query | No | Time range relative to present time. `-60` means last minute. `-3600` means last hour. |
| `start_id` | integer | query | No | Specifies first job to include. `-5` means latest 5 entries. `5` means log index 5. `0` means first entry of the joblog. |
| `count` | integer | query | No | Specifies number of jobs to include in the output. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Success — a list of Job Log Entries and their Attributes Hash. |

---

### GET /live/api/v5/accounting/:id

Retrieve Job Log Entry settings.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `:id` | string | path | Yes | ID of the job log entry to retrieve. |
| `key[]` | string | query | No | An array of selected keys. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Success — the Job Log Entry and its Attributes Hash. |

---

## Licenses

### GET /live/api/v5/licenses

Returns information on Fiery API license.

**Parameters**

_None_

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Returns information on Fiery API license. |

---

### POST /live/api/v5/licenses

Activate Fiery API license feature, using License Activation Code or request file.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `code` | string | query | Yes | License Activation Code. |
| `file` | binary | query | Yes | Request file. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | License feature successfully activated. |
| 400 | Unable to activate license feature. |

---

### GET /live/api/v5/licenses/:id

Returns details of Fiery API license.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `:id` | string | path | Yes | License feature id. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Success. |
| 400 | The service does not understand the content of the request. |
| 404 | License feature not found. |

---

### DELETE /live/api/v5/licenses/:id

Deactivate Fiery API license feature.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `:id` | string | path | Yes | License Feature id. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 204 | License feature successfully deactivated. |
| 400 | The service does not understand the content of the request. |

---

### POST /live/api/v5/licenses/requestfiles

Get Fiery API license request file for offline activation.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `code` | string | query | Yes | License Activation Code. |
| `requestType` | string | query | Yes | Type of the request: `activation` or `deactivation`. |
| `productType` | string | query | Yes | Type of the product, must be `fiery api`. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | License request file successfully created. |
| 400 | Unable to create license request file. |

---

## Consumables

### GET /live/api/v5/consumables

Reports information about the supply of paper, tray, and toner on the print engine.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `key[]` | string | query | No | A key or an array of keys to retrieve. e.g. `key[]=trays&key[]=colorants`. If not specified, returns all keys. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Returns an object containing colorants and trays/webs information. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |

---

## Devices

### GET /live/api/v5/devices(/:id)

Returns information about the print device connected to the Fiery server and its currently printing/ripping jobs' progress.

**Parameters**

_None_

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Success — returns device info and currently ripping/printing jobs progress. e.g. `{:id=>deviceid, :jobs=>[...], :name=>devicename, :online=>true/false, :prettyname=>deviceprettyname, :status=>device status}` |

---

## Presets

### GET /live/api/v5/presets

Lists presets and their attributes.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `key[]` | string | query | No | A key or an array of keys to retrieve. |
| `filter` | string | query | No | Filter parameters as key-value pairs. e.g. `PageSize=Letter&EFDuplex=TopTop,TopDown,available=true` |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Returns a list of presets and their attributes, or selected attributes when a key filter is passed. Empty if nothing found. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |

---

### GET /live/api/v5/presets/:id

Returns the attribute settings of a preset.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `id` | string | path | Yes | Preset's id. |
| `key[]` | string | query | No | A key or an array of keys to retrieve. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | The preset's attributes or selected attributes. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |
| 404 | The specified resource is not found. |

---

## Queues

### GET /live/api/v5/queues

Lists the printer queues (logical printers) configured on the Fiery.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `key[]` | string | query | No | A key or an array of keys to retrieve. |
| `filter` | string | query | No | Filter parameters as key-value pairs. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Returns a list of queues and their attributes, or selected attributes when a key filter is passed. Empty if nothing found. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |

---

### GET /live/api/v5/queues/:id

Returns the attributes of a printer queue (logical printer).

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `id` | string | path | Yes | Queue's id. |
| `key[]` | string | query | No | A key or an array of keys to retrieve. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | The queue's attributes or selected attributes. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |
| 404 | The specified resource is not found. |

---

## Properties

### GET /live/api/v5/properties

List of all the ppd, pdd, netcfg, dict.ppd, email.pdd keys and values.

**Parameters**

_None_

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Array of property objects that include `[id, value, group, ppdtype, query, scopes, values, value_attributes, constraints]` |
| 206 | Incomplete data. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |

---

### GET /live/api/v5/properties/:id

Retrieve a particular property.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `:id` | string | path | Yes | ID of the property to retrieve. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Hash of Property Object that includes `[id, value, group, ppdtype, query, scopes, values, value_attributes, constraints]` |
| 206 | Incomplete data. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |

---

## Paper Catalog

### GET /live/api/v5/papercatalog

Retrieve a list of papercatalogs on the Fiery.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `key[]` | string | query | No | One or array of keys to fetch. |
| `{filterkey}={filtervalue}` | Hash | query | No | A hash of filterkey/filtervalue pairs as filters. e.g. `MediaType=Paper&FeedDirection=SEF` |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | A list of papercatalog objects. |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |

---

### GET /live/api/v5/papercatalog/:id

Retrieve a particular papercatalog entry.

**Parameters**

| Name | Type | Located In | Required | Description |
|------|------|------------|----------|-------------|
| `:id` | string | path | Yes | ID of the papercatalog entry to retrieve. |
| `key` | String | query | No | One or array of keys to fetch. |

**Response Status Codes**

| Code | Description |
|------|-------------|
| 200 | Papercatalog object with JDF keys `[id, name, Dimension, FeedDirection, ProductID, ...]` |
| 401 | The client is not authorized to access the requested API. |
| 403 | The client is not allowed to access the requested API. |
