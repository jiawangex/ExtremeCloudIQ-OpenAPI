# ExtremeCloud IQ API Design Guidelines

## Introduction
This documentation is intended to be used by ExtremeCloud IQ architects and developers as guidelines for designing 
any REST API exposed publicly.
Private or internal APIs SHOULD also follow these guidelines for providing the same level of consistency to 
internal service consumers and simplify the process if the internal APIs are subsequently exposed publicly.

## Conventions used

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", 
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).

## Verifying compliance
We leverage both `Redocly` and `Spectral` with our custom ruleset to validates OpenAPI definitions against a subset of 
the standards defined in this documentation and identifies areas of non-compliance with the OpenAPI specification or 
the guidelines in this documentation.

## API Design Guidelines

### Use nouns instead of verbs in endpoint paths
We shouldn’t use verbs in our endpoint paths. Instead, we should use the nouns which represent the resource that the endpoint that we’re retrieving or manipulating as the pathname.

### Use plural forms
We should use plural forms for all resource names. Plural forms make the requests more consistent and intuitive. For example, `GET /devices` is more intuitive than `GET /device`.

### Use lower-case format for URLs
Because URLs are case-sensitive, all APIs are canonically lower-case to make them consistent and avoid unnecessary errors.

### Use hyphens (-) instead of underscores (_) in URLs
Hyphens is much readable than underscores in URLs.

### Mapping CRUD operations to standard HTTP verbs
Use standard HTTP verbs for CRUD operations to achieve simple and consistent APIs.
| HTTP Verb | Description                                                             |
| --------- | ----------------------------------------------------------------------- |
| `GET`     | Retrieving resources.                                                   |
| `POST`    | Creating resources or calling business operations.                      |
| `PUT`     | Replacing resources or creating resources if the resources don't exist. |
| `PATCH`   | Updating resources with partial JSON data.                              |
| `DELETE`  | Deleting resources.                                                     |

### Use consistent format for custom operations
For those actions cannot fit into CRUD operations, we will use `POST {resource-name}/:{action}` format.
* The HTTP verb should always be `POST`
* Put the action name at the end of the path and add `:` as prefix to make it clear for API users

For example, `POST /devices/:reboot` to reboot devices.

### Handle errors gracefully
To eliminate confusion for API users when an error occurs, we should handle errors gracefully and return HTTP response codes that indicate what kind of error occurred. This gives maintainers of the API enough information to understand the problem that’s occurred. We don’t want errors to bring down our system, so we can leave them unhandled, which means that the API consumer has to handle them.

### Return standard error codes
The HTTP Status Codes are used to indicate the status of the executed operations.

* Success

| Status Code | Description | Use Cases                                                                       |
|-------------|-------------|---------------------------------------------------------------------------------|
| 200         | OK          | Standard response for successful HTTP requests.                                 |                                 
| 201         | Created     | The request has been fulfilled, resulting in the creation of a new resource.    |    
| 202         | Accepted    | The request is queued for background processing (async tasks).                  |  
| 204         | No Content  | The server successfully processed the request and is not returning any content. | 

* Client Error

| HTTP Status | Description            | Use Cases                                                                                                                                                                                                                                                           | Client recovery policy                                                                                                                                                                                                                |
|-------------|------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400         | Bad Request            | The API endpoint exists, but the HTTP request syntax or payload is incorrect, detail will be given in response.                                                                                                                                                     | Do not retry. Fix the request.                                                                                                                                                                                                        |                                                                                                                                                                                                        
| 401         | Unauthorized           | The server can be reached and understood the request, but refuses to take any further action, because the client must provide authorization.If the client has provided authorization, the server is indicating the provided authorization is unsuitable or invalid. | If the user has not supplied authorization information, prompt them for the appropriate credentials.If the user has supplied authorization information, inform them their credentials were rejected and optionally prompt them again. | 
| 403         | Forbidden              | The server can be reached and understood the request, but refuses to take any further action, because it is configured to deny access for some reason to the requested resource by the client.                                                                      | Do not retry. Fix the request.                                                                                                                                                                                                        |                                                                                                                                                                                                     
| 404         | Not Found              | The API endpoint doesn’t exist or resource doesn’t exist.                                                                                                                                                                                                           | Do not retry. Fix the request.                                                                                                                                                                                                        |                                                                                                                                                                                                    
| 415         | Unsupported Media Type | The request entity has a media type which the server or resource does not support.                                                                                                                                                                                  | Do not retry. Fix the request.                                                                                                                                                                                                        |                                                                                                                                                                                                  
| 429         | Too Many Requests      | The request entity has a media type which the server or resource does not support.                                                                                                                                                                                  | Retry after a while.                                                                                                                                                                                                                  |                                                                                                                                                                                                  

* Server Error

| HTTP Status Code | Description           | Use Cases                                                       | Client recovery policy                                                   |
|------------------|-----------------------|-----------------------------------------------------------------|--------------------------------------------------------------------------|
| 500              | Internal Server Error | Server side Exception caught, detail will be given in response. | Ask user to retry later or the client automatically calls retry request. |
| 503              | Service Unavailable   | The server is temporary unavailable.                            | Ask user to retry later or the client automatically calls retry request. | 

### Allow filtering, sorting, and pagination
The databases behind a REST API can get very large. Sometimes, there’s so much data that it shouldn’t be returned all at once because it’s way too slow or will bring down our systems. Therefore, we need ways to filter items.

We also need ways to paginate data so that we only return a few results at a time. We don’t want to tie up resources for too long by trying to get all the requested data at once.

Filtering and pagination both increase performance by reducing the usage of server resources. As more data accumulates in the database, the more important these features become.

### Field selections
Our APIs should provide API users the flexibility to fetch only the required details instead of everything. No over-fetching or under-fetching. Field selections are helpful for low-computing devices (such as mobile or IoT) as it allows them to save on bandwidth and improve the overall network transmission speed.

You can do this by offering ways within the request to include or exclude data of specific fields. For example: `/devices?fields[]=name,mac_address,created_at` and `/devices?views=basic`

### Maintain good security practices
Most communication between client and server should be private since we often send and receive private information. Therefore, using SSL/TLS for security is a must.

People shouldn’t be able to access more information that they requested. For example, a normal user shouldn’t be able to access information of another user. They also shouldn’t be able to access data of admins.

To enforce the principle of the least privilege, we need to add role checks either for a single role, or have more granular roles for each user.

Beside RBAC security, we also need to consider data-level security.

### Keep APIs both forward and backward compatible
For maximum versatility, REST APIs should be both forward and backward compatible.

From the client perspective, forward compatibility means an older client can still work with a more recent REST API version; backward compatibility means that a more recent client can still work with an older REST API version.

Below are some common breaking changes:
* Delete/Rename path
* Delete/Rename parameters
* Add a constraint on a parameter (like isRequired)
* Delete/Rename fields in the response payload

### API deprecation
In some times, we may remove some APIs for business requirement or cost consideration. We will communicate the deprecation plan beforehand, so that API users can have 3 month to 1 year sunset or migration period.

### API versioning
If it cannot keep both forward and backward compatible, it's better to define a brand-new API or add a new version API for the use cases. If decide to add a new version API, please place the REST API version number in front of an existing name.

For example, we can define `POST /v2/devices/:onboard` for the new device onboarding API.