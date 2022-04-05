# VCC Rest API Standards

Excerpt from [here](https://confluence.8x8.com:8443/display/CCE/VCC+Rest+API+Standards)


## General guidelines

### 100. Follow API first principle

### 101. Provide API specification using OpenAPI
Use OpenAPI 3.0 and YAML file format.

### 103. Write APIs in U.S. English



## Meta information

### 218. Contain API meta information
API specification must contain:
- `#/info/title`
- `#/info/version`
- `#/info/description`
- `#/info/contact/{name,url,mail}`
- `#/info/x-audience`

### 116. Use Semantic Versioning
Comply to Semantic Versioning 2.0 rules 1 to 8 and 11 restricted to the format `<MAJOR>.<MINOR>.<PATCH>`

### 219. Provide API audience
Each API must be classified with respect to the intended target audience supposed to consume the API:
- `component-internal`
- `business-unit-internal`
- `company-internal`
- `external-partner`
- `external-public`



## Security

### 104. Secure endpoints with OAuth 2.0

### 225. Follow naming convention for permissions (scopes)
As long as the functional naming is not supported for permissions, permission names in APIs must conform to the following naming pattern:
```
<permission> ::= <standard-permission> |  -- should be sufficient for majority of use cases
                 <resource-permission> |  -- for special security access differentiation use cases
                 <pseudo-permission>      -- used to explicitly indicate that access is not restricted

<standard-permission> ::= <application-id>.<access-mode>
<resource-permission> ::= <application-id>.<resource-name>.<access-mode>
<pseudo-permission>   ::= uid

<application-id>      ::= [a-z][a-z0-9-]*  -- application identifier
<resource-name>       ::= [a-z][a-z0-9-]*  -- free resource identifier
<access-mode>         ::= read | write    -- might be extended in future
```



## Compatibility

### 106. Don't break backward compatibility
There are two techniques to change APIs without breaking them:
- follow rules for compatible extensions
- introduce new API versions and still support older versions
We strongly encourage using compatible API extensions and discourage versioning.

### 108. Prepare clients to not crash on compatible API extensions

### 110. Always return JSON objects as top-level data structures to support extensibility

### 111. Treat OpenAPI definitions as open for extension by default
To be revisited.

### 113. Avoid versioning
When changing RESTful APIs, do so in a compatible way and avoid generating additional API versions.

### 115. Use URI versioning



## Deprecation

### 185. Obtain approval of clients
Before shutting down an API (or version of an API) the producer must make sure that all clients have given their consent to shut down the endpoint. 

### 186. External partners must agree on deprecation timespan

### 187. Reflect deprecation in API definition

### 188. Monitor usage of deprecated APIs
Owners of APIs used in production must monitor usage of deprecated APIs until the API can be shut down in order to align deprecation and avoid uncontrolled breaking effects.

### 191. Not start using deprecated APIs



## JSON guidelines

### 118. Property names must be ASCII snake_case

### 122. Boolean property values must not be null

### 124. Empty array values should not be null

### 126. Date property values should conform to RFC 3339

### 128. Standards could be used for Language, Country and Currency



## API naming

### 223. Use functional naming schema
Follow the functional naming schema for hostnames, permission names, and event names in APIs as follows:

| Functional naming | Audience                                 |
|-------------------|------------------------------------------|
| must              | external-public, external-partner        |
| should            | company-internal, business-unit-internal |
| may               | component-internal                       |

To conduct the functional naming schema, a unique functional-name is assigned to each functional component. It is built of the domain name of the functional group the component is belonging to and a unique a short identifier for the functional component itself:
```
<functional-name>       ::= <functional-domain>-<functional-component>
<functional-domain>     ::= [a-z][a-z0-9]*  -- managed functional group of components
<functional-component>  ::= [a-z][a-z0-9-]* -- name of owning functional component
```

### 224. Follow naming convention for hostnames

Hostnames in APIs must, respectively should conform to the functional naming depending on the audience as follows
```
<hostname>         	::= <functional-hostname> | <application-hostname>
<functional-hostname>  ::= <functional-name>.8x8.com
``` 
 
The following application specific legacy convention is only allowed for hostnames of component-internal APIs:
```
<application-hostname> ::= <application-id>.<organization-unit>.8x8.com
<application-id>   	::= [a-z][a-z0-9-]*  -- application identifier
<organization-id>  	::= [a-z][a-z0-9-]*  -- organization unit identifier, e.g. team identifier
```

### 129. Use lowercase separate words with hyphens for path segments

### 130. Use snake_case (never camelCase) for query parameters

### 132. Prefer Hyphenated-Pascal-Case for HTTP header fields

### 134. Pluralize resource names

### 136. Avoid trailing slashes

### 137. Stick to conventional query parameters
If you provide query support for searching and filtering, you must stick to the following naming conventions: query, fields, embed.



## Resources

### 138. Avoid actions - think about resources
REST is all about your resources, so consider the domain entities that take part in web service interaction, and aim to model your API around these using the standard HTTP methods as operation indicators. 
For instance, if an application has to lock articles explicitly so that only one user may edit them, create an article lock with PUT or POST instead of using a lock action. 

### 141. Keep URLs verb-free
The API describes resources, so the only place where actions should appear is in the HTTP methods. 
In the extraordinary case where a resource cannot be identified (or things become too complex) it is allowed to use verbs. 
Think hard before falling back to verbs. 

### 142. Use domain-specific resource names

### 228. Use URL-friendly resource identifiers

### 143. Identify resources and sub-resources via path segments
Basic URL structure:
- `/{resources}/[resource-id]/{sub-resources}/[sub-resource-id]`
- `/{resources}/[partial-id-1][separator][partial-id-2]`

Examples:
- `/carts/1681e6b88ec1/items`
- `/carts/1681e6b88ec1/items/1`
- `/customers/12ev123bv12v/addresses/DE_100100101`
- `/content/images/9cacb4d8`




## HTTP requests

### 148. Use HTTP methods correctly
Be compliant with the standardised HTTP method semantics summarised as follows:

#### GET
GET requests are used to read either a single or a collection resource.
GET requests for individual resources will usually generate a 404 if the resource does not exist
GET requests for collection resources may return either 200 (if the collection is empty) or 404 (if the collection is missing, url is not correct)
GET requests must NOT have a request body payload (see GET With Body)

#### PUT
PUT requests are used to update (in rare cases to create) entire resources – single or collection resources.
PUT requests are usually applied to single resources, and not to collection resources, as this would imply replacing the entire collection
PUT requests are usually robust against non-existence of resources by implicitly creating before updating
on successful PUT requests, the server will replace the entire resource addressed by the URL with the representation passed in the payload (subsequent reads will deliver the same payload)
successful PUT requests will usually generate 200 or 204 (if the resource was updated – with or without actual content returned), and 201 (if the resource was created)

#### POST
POST requests are idiomatically used to create single resources on a collection resource endpoint, but other semantics on single resources endpoint are equally possible.
on a successful POST request, the server will create one or multiple new resources and provide their URI/URLs in the response
successful POST requests will usually generate 200 (if resources have been updated), 201 (if resources have been created), 202 (if the request was accepted but has not been finished yet), and exceptionally 204 with Location header (if the actual resource is not returned).

#### PATCH
PATCH requests are used to update parts of single resources, i.e. where only a specific subset of resource fields should be replaced.
PATCH requests are usually applied to single resources as patching entire collection is challenging
PATCH requests are usually not robust against non-existence of resource instances
on successful PATCH requests, the server will update parts of the resource addressed by the URL as defined by the change request in the payload
successful PATCH requests will usually generate 200 or 204 (if resources have been updated with or without updated content returned).

#### DELETE
DELETE requests are used to delete resources. 
DELETE requests are usually applied to single resources, not on collection resources, as this would imply deleting the entire collection
successful DELETE requests will usually generate 200 (if the deleted resource is returned) or 204 (if no content is returned)
failed DELETE requests will usually generate 404 (if the resource cannot be found) or 410 (if the resource was already deleted before and it is important or the business domain to be able to express that a resource existed previously; e.g. a Username cannot be reused) 

#### HEAD
HEAD requests are used to retrieve the header information of single resources and resource collections.
HEAD has exactly the same semantics as GET, but returns headers only, no body.

### 149. Fulfill common method properties
Request methods in RESTful services can be…
- safe - the operation semantic is defined to be read-only, meaning it must not have intended side effects, i.e. changes, to the server state.
- idempotent - the operation has the same intended effect on the server state, independently whether it is executed once or multiple times. Note: this does not require that the operation is returning the same response or status code.
- cacheable - to indicate that responses are allowed to be stored for future reuse. In general, requests to safe methods are cachable, if it does not require a current or authoritative response from the server.

| Method  | Safe | Idempotent | Cacheable |
|---------|------|------------|-----------|
| GET     | Yes  | Yes        | Yes       |
| HEAD    | Yes  | Yes        | Yes       |
| POST    | No   | No         | May       |
| PUT     | No   | Yes        | No        |
| PATCH   | No   | No         | No        |
| DELETE  | No   | Yes        | No        |
| OPTIONS | Yes  | Yes        | No        |
| TRACE   | Yes  | Yes        | No        |


### 226. Document implicit filtering
If the access to some fields of a resources or some resources of a collection are limited by authorization policies, these must be documented in the API design document.

## HTTP status codes and errors

### 151. Specify success and error responses
Unless a response code conveys application-specific functional semantics or is used in a non standard way that requires additional explanation, multiple error response specifications can be combined using the `default` OpenAPI response definition.

### 150. Use standard HTTP status codes
Refer to [RFC7231](https://tools.ietf.org/html/rfc7231#section-6) and [RFC6585](https://tools.ietf.org/html/rfc6585).
Only if the HTTP status code is not in the list below or its usage requires additional information aside the well defined semantic, the API specification must provide a clear description of the HTTP status code in the response.

#### Success codes
| Code | Meaning                                           | Methods                          |
|------|---------------------------------------------------|----------------------------------|
| 200  | Standard success response                         | `<all>`                          |
| 201  | Created                                           | `POST`, `PUT`                    |
| 202  | Accepted (Request to be processed asynchronously) | `POST`, `PUT`, `PATCH`, `DELETE` |
| 204  | No content                                        | `PUT`, `PATCH`, `DELETE`         |
| 207  | Multi-status (see rule 152)                       | `POST`                           |

#### Redirection codes
| Code | Meaning                                                      | Methods                          |
|------|--------------------------------------------------------------|----------------------------------|
| 301  | Moved permanently                                            | `<all>`                          |
| 303  | See Other (response can be found in another URL using `GET`) | `POST`, `PUT`, `PATCH`, `DELETE` |
| 304  | Not modified                                                 | `GET`                            |

#### Client side error codes
| Code | Meaning                                                   | Methods                          |
|------|-----------------------------------------------------------|----------------------------------|
| 400  | Bad request                                               | `<all>`                          |
| 401  | Unauthorize                                               | `<all>`                          |
| 403  | Forbidden                                                 | `<all>`                          |
| 404  | Not found                                                 | `<all>`                          |
| 405  | Method not allowed                                        | `<all>`                          |
| 405  | Not acceptable                                            | `<all>`                          |
| 408  | Timeout                                                   | `<all>`                          |
| 409  | Conflict                                                  | `POST`, `PUT`, `PATCH`, `DELETE` |
| 410  | Gone                                                      | `<all>`                          |
| 412  | Precondition failed (`If-Match` if the condition failed)  | `PUT`, `PATCH`, `DELETE`         |
| 415  | Unsupported media type                                    | `POST`, `PUT`, `PATCH`, `DELETE` |
| 423  | Locked                                                    | `PUT`, `PATCH`, `DELETE`         |
| 428  | Precondition required                                     | `<all>`                          |
| 429  | Too many requests                                         | `<all>`                          |

#### Server side error codes
| Code | Meaning                                                                        | Methods |
|------|--------------------------------------------------------------------------------|---------|
| 500  | Internal server error                                                          | `<all>` |
| 501  | Not implemented                                                                | `<all>` |
| 503  | Service unavailable                                                            | `<all>` |
| 504  | Gateway timeout (a service dependency is not available, **internal use only**) | `<all>` |

### 220. Use most specific HTTP status codes

### 152. Use code 207 for batch or bulk requests
A batch defines a collection of requests triggering independent processes, a bulk defines a collection of independent resources created or updated together in one request. In this case services may be in need to signal multiple response codes for each part of a batch or bulk request.
A batch or bulk request always has to respond with HTTP status code 207, unless it encounters a generic or unexpected failure before looking at individual parts.
A batch or bulk response with status code 207 always returns a multi-status object containing sufficient status and/or monitoring information for each part of the batch or bulk request.

### 153. Use code 429 with headers for rate limits
APIs that wish to manage the request rate of clients must use the 429 response code, if the client exceeded the request rate. Such responses must also contain header information providing further details to the client. 

### 176. Use Problem JSON
Operations should return a agreed upon `Problem` (together with a suitable status code) when any problem occurred during processing and you can give more details than the status code itself can supply, whether it be caused by the client or the server (i.e. both for `4xx` or `5xx` error codes).

### 177. Do not expose stack traces


## Performance

### 158. Allow optional embedding of sub-resources
In cases where clients know upfront that they need some related resources they can instruct the server to prefetch that data eagerly.
For example, given we have an order containing item IDs we can have an endpoint like `GET /order/123?embed=(items)` whose response is:
```json
{
  "id": "123",
  "_embedded": {
    "items": [
      {
        "item_id": 1,
        "price": {
          "amount": 71.99,
          "currency": "EUR"
        }
      }
    ]
  }
}
```

### 227. Document cacheable GET, HEAD and POST endpoints
Client side as well as transparent web caching should be avoided, unless the service supports and requires it to protect itself.
API providers and consumers should always set the `Cache-Control` header to `{Cache-Control-no-store}` and assume the same setting, if no `Cache-Control` header is provide.
If your service really requires to support caching, document the endpoints appropriately.


## Pagination

### 159. Support pagination


## Hypermedia

### 162. Use REST maturity level 2

### 217. Use full, absolute URI
Links to other resources must always use full, absolute URI.

### 164. Use common hypertext controls
When embedding links to other resources into representations, you must use the common hypertext control object.
The schema for hypertext controls can be derived from this model:
```
HttpLink:
  description: A base type of objects representing links to resources.
  type: object
  properties:
    href:
      description: Any URI that is using http or https protocol
      type: string
      format: uri
  required:
    - href
```

### 166. Not use link headers with JSON entities
Links must be directly embedded in JSON payloads.


## Data formats

### 167. Use JSON to encode structured data

### 169. Use standard date and time formats
In the JSON payload date and time format must conform to RFC 3339.
In the HTTP headers must conform to RFC 7231.

### 171. Define format for type number and integer
Whenever an API defines a property of type number or integer, the precision must be defined by the format as follows to prevent clients from guessing the precision incorrectly, and thereby changing the value unintentionally.

## Common data types

### 174. Use common field names and semantics
There are some data fields that come up again and again in API data:
- `id`: the identity of the object.
- `xyz_id`: an attribute within one object holding the identifier of another object must use a name that corresponds to the type of the referenced object or the relationship to the referenced object followed by `_id`
- `created`: when the object was created.
- `modified`: when the object was updated.
- `type`: the kind of thing this object is.
- `etag`: the ETag of an embedded sub-resource.


## Common headers

### 178. Use Content-* headers correctly
Content or entity headers are headers with a `Content-` prefix. They describe the content of the body of the message and they can be used in both, HTTP requests and responses. 


## Proprietary headers

### 183. Use only the specified proprietary 8x8 headers
As a general rule, proprietary HTTP headers should be avoided. Still they can be useful in cases where context needs to be passed through multiple services in an end-to-end fashion.
The guidelines restrict which `X-` headers can be used and how they are used.
Allowed headers:
- `X-Flow-ID`
- `X-Tenant-ID`
- `X-Sales-Channel`
- `X-Frontend-Type`
- `X-device-Type`
- `X-device-OS`
- `X-App-Domain`

### 184. Propagate proprietary headers
All 8x8’s proprietary headers are end-to-end headers.
All headers specified above must be propagated to the services down the call chain. The header names and values must remain unchanged.

### 233. Use X-Flow-ID
The `Flow-Id` is a generic parameter to be passed through service APIs and events and written into log files and traces.
Main use case of `Flow-Id` is to track service calls of our SaaS fashion commerce platform and initiated internal processing flows

## API operation

### 192. Publish OpenAPI specification