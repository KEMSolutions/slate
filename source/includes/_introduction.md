# Introduction

```php
/*
PHP sample code requires the Guzzle PHP client 
(https://guzzle.readthedocs.org/).
*/
```

```python
"""
Python sample code requires the requests library
(http://docs.python-requests.org/).
"""
```

The KEM API is organized around [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). All API access is over HTTPS, and accessed from the `api.kem.guru` domain. All data is sent and received as JSON. Blank fields are included as `null` instead of being omitted.

To make the KEM API as explorable as possible, accounts have test-mode API keys as well as production-mode API keys. These keys can be active at the same time. Orders created with test-mode credentials will never be fulfilled and are explorable from the KEM Console web application or through API calls using the same development key.




## Authentication

```shell
echo -n '{"payload":"Hello, world!"}9Ko2BKQ8DRQSNjIjEVNwgxcRS1uvNcVyYeE63UZPPqxW3q8l6jdjfmpTGhAqFJW' | openssl dgst -sha512 -binary | openssl enc -base64
```

```php
<?php
$request_body = '{"payload":"Hello, world!"}';
$api_secret_key = '9Ko2BKQ8DRQSNjIjEVNwgxcRS1uvNcVyYeE63UZPPqxW3q8l6jdjfmpTGhAqFJW';
$concatenated_string = $request_body . $api_secret_key;
$signature = base64_encode(hash('sha512', $concatenated_string, true));

echo $signature;
```

```python
import hashlib, base64

request_body = '{"payload":"Hello, world!"}'
api_secret_key = '9Ko2BKQ8DRQSNjIjEVNwgxcRS1uvNcVyYeE63UZPPqxW3q8l6jdjfmpTGhAqFJW'

m = hashlib.sha512()
m.update(request_body + api_secret_key)
signature =  base64.b64encode(m.digest())

print(signature)
```

You authenticate to the KEM API by signing your request with your API secret key and providing your API user ID.

KEM Console expects for the request signature key and your API user ID to be included in all API requests in the two following headers:

- `X-Kem-User:`
- `X-Kem-Signature:`

All KEM API request must be signed, and this signature included in the `X-Kem-Signature` header. The value of this header is the base64 encoded SHA512 signature of a string consisting of the body of the request (no whitespace, can be empty for GET requests) followed immediately by your API secret key.

For example, this request body:

`
{"payload":"Hello, world!"}
`

Signed with this API secret key:

`
9Ko2BKQ8DRQSNjIjEVNwgxcRS1uvNcVyYeE63UZPPqxW3q8l6jdjfmpTGhAqFJW
`

Has the following signature:

`
WHq3XTcyhAWUuFmV93cnHsE3AdsCyOEp3rQlzbfaRUhD2vsx+3YsPT4V+dtMi4WCO9X76iCSWV69IaTRKBYjYA==
`

Authenticating with invalid credentials will return `403 Forbidden`.


## Content type

`POST` and `PUT` requests must include a header, describing the body's content type:

`Content-Type: application/json`

Failure to include this header will result in a `415 Unsupported Media Type` error.

By default, all endpoint responses provide data as JSON in the response body and include a `Content-Type: application/json` header.

## Endpoints

KEM Console API endpoints are hosted from the base URL `https://api.kem.guru/api/`. For example, the Products endpoint's full path is `https://api.kem.guru/api/{version}/products`.

Unencrypted HTTP endpoints are not supported and will return a `426 Upgrade Required` error.

### API versions

An endpoint's API version is included in its path. Bug fixes and minor feature additions might be made to an endpoint's behavior without advancing its version number. This can include adding optional parameters or response fields. To prevent future compatibility issues, your application should be prepared to receive response fields beyond those currently returned by a given endpoint.

Functionality is never removed from a particular version of an endpoint, nor do field names or types change.

Currently, all KEM API endpoints have a single version (v1).

### Endpoint names and methods

An endpoint's name indicates the type of data it handles and the restful action it performs on that data. The most common actions are:

|  Action  |  Method  |                                                                                                                                  Description                                                                                                                                  |
|:--------:|:--------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| Create   | `POST`   | Creates and persists an entity of the corresponding type.                                                                                                                                                                                                                     |
| List     | `GET`    | Returns summary information for all entities that match query parameters you provide. To get comprehensive information for a particular entity, first get the entity's `id` with the appropriate List endpoint, then provide the `id` to the corresponding Retrieve endpoint. |
| Retrieve | `GET`    | Provides comprehensive information for the single entity that matches the identifier you provide.                                                                                                                                                                             |
| Update   | `PUT`    | Modifies the existing entity that matches the identifier you provide.                                                                                                                                                                                                         |
| Delete   | `DELETE` | Deletes the existing entity that matches the identifier you provide. Deleted entities cannot be retrieved or undeleted. Currently, no endpoint supports this method.                                                                                                          |


## Language

Much of the user facing content available on the API is available in more than a single locale. You should therefore include your client's preferred locale or language in a `Accept-Language:` to get localized product descriptions, category name, etc. in the client's language.

A list of available [locale](#locales) objects is returned in the GET [`Store`](#stores) request. You can either use the locale's `id` or `language` in your request.

If a language is available in more than one locale and you only send the `language` and not the complete locale `id`, the API will use the first conforming locale available. While this locale swap might not be much of a problem for some languages (*en-CA* and *en-US*, for example), passing the entire locale `id` is the sensible thing to do. Not only being respectful of dialects and regional particularities is a first step towards a stellar user experience, but some locales with the same language code are not even entirely mutually intelligible (*zh-CN* and *zh-HK*, for example).

Sending an unsupported locale or language in the `Accept-Language:` header will result in a `400 Bad Request` error (see [Locales](#locales) for a list of locales accepted by the KEM API).

## Pagination

Requests that return multiple items will be paginated to 16 items by default. You can specify further pages with the `&page` parameter. For some resources, you can also set a custom page size up to 100 with the `&per_page` parameter.

Note that page numbering is 1-based and that omitting the `&page` parameter will return the first page.

### Link header

The pagination info is included in the `Link:` header. It is important to follow these `Link:` header values instead of constructing your own URLs.

`<https://api.kem.guru/api/1/products/search?q=vitamine+c&page=186>; rel="last", <https://api.kem.guru/api/1/products/search?q=vitamine+c&page=2>; rel="next"`

The possible `rel` values are:

| Name    | Description                                              |
|---------|----------------------------------------------------------|
| `next`  | Shows the URL of the immediate next page of results.     |
| `last`  | Shows the URL of the last page of results.               |
| `first` | Shows the URL of the first page of results.              |
| `prev`  | Shows the URL of the immediate previous page of results. |

### Total count

Requests that return multiple items will also include a `X-Total-Count:` header informing you of how many items are available for the given resource.

`X-Total-Count: 2969`

## Parameters

The way you provide parameters to a Connect API request depends on the HTTP method of the request.

### `GET` and `DELETE` requests

For `GET` and `DELETE` requests, you provide parameters in a query string you append to your request's URL. For example, you provide the order parameter to the Search Products endpoint like so:

`https://api.kem.guru/api/1/products/?order=price&filters=brands:314`

Values for query parameters must be URL-escaped. For example, to provide the value *Vitamine C for kids* as the *q* parameter of the Search Products endpoint, you specify the following:

`&q=Vitamin%20C%20for%20kids`


### `POST` and `PUT` requests

For `POST` and `PUT` requests, you instead provide parameters as JSON in the body of your request.

For example, the body of a request to the Update Customer endpoint could look like this:

`
{
    "email": "john.smith@example.com",
    "name": "John Smith",
    "language": "en”
}
`



## Rate limiting

A single IP address can make up to 30 requests per minute. You can avoid hitting this rather strict limit by passing your clients' IP addresses in a `X-Forwarded-For:` header.

You can check the returned HTTP headers of any API request to see your current rate limit status:

| Header name            | Description                                                                                     |
|------------------------|-------------------------------------------------------------------------------------------------|
| X-Rate-Limit-Limit     | The maximum number of requests that the consumer is permitted to make per minute (default: 30). |
| X-Rate-Limit-Remaining | The number of requests remaining in the current rate limit window.                              |
| X-Rate-Limit-Reset     | The number of seconds left in the current rate limit window.                                    |

Once you go over the rate limit you will receive a `429 Too Many Requests` error, until the next rate limit window. If your application is frequently running over the rate limit and you already include your clients' IPs in the `X-Forwarded-For:` header, please contact us so we can customize your rate limit to your needs.


