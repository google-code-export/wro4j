# Introduction #
The API will be implemented using Spring MVC 3.0 and will use REST (JSON & XML). Will be deployed on the following domain: api-wro4j.appsport.com

The API will cover the following "components": resourceProcessor, ...(more to define).


# ResourceProcessors API #
## Get All Available Resource Processors ##
Expose all available processors. This call will return a list of resource processors alias names.
```
GET /resourceProcessor/list
```

**Response Example (JSON):**
```
["jsMin","cssMin","bomStripper"]
```

## Get Resource Processor Summary Details ##
```
GET /resourceProcessor/summary/:alias
```
**Request Parameters:**
  * **alias** - the name of the resource processor

Example:
```
GET /resourceProcessor/summary/jsMin
```


**Response Example (JSON):**
```
[
  {
    "alias": "jsMin"
    "supportedResourceType": "JS"
  }
]
```

## Apply Resource Processor ##
Apply the processing on some resource
```
POST /resourceProcessor/apply/:alias
```
**Request Parameters:**
  * **alias** - the name of the resource processor
**Parameters:**
  * **code\_url** - the url of the resource to process
  * **code** - code to process
  * **download** - When given, this should contain a filename. The server will add a Content-Disposition header to the response, which causes typical browsers to download the response under the given filename.

**Response:**
  * The response body will contain the compressed code in case of a successful request, or an error message when returning an error HTTP status code.