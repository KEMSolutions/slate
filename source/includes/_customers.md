# Customers

## Customers Management Overview

KEM partners can view, create and modify customers with these endpoints. Most of the customer management business logic is expected to take place on your application.

<aside class="warning">By itself, KEM API doesn't offer any account management mechanism (registration, authentication, etc.). If you need help implementing these functionalities, take a look at Boukem 2's sample code. </aside>


| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | integer | KEM unique ID for this customer.                                                                                 |
| email    | string  | The customer's email address                                                                        |
| postcode | string  | Zip/Postal Code                                                                                                  |
| name     | string  | The customer's display name                                                                                      |
| locale   | object  | The customer's preferred [locale](#locales). Must be a supported language obtained in the [list locales](#list-locales) object. Note that this locale might not be a locale that is supported by your store (listed in the [retrieve store](#retrieve-store)). Your application should try to handle gracefully such a scenario by presenting another supported locale. |
| language | string  | The customer's preferred language. **deprecated** |
| phone    | string  | The customer's phone number, preferably in a standardized format (such as +15145555555).                         |
| metadata | string  | An optional variable you can use for any purpose relevant to your application (soft limit of 1024 characters). Never store plaintext passwords in a database, especially not ours. |
|production| boolean | When `production` is false, the customer was created with a development API key and should be treated as a test customer. A test customer can create production orders if the API key used to create the order is a production one; the production value of the test customer will then change to *true*. In the same way, a `production : true` customer can become a test customer if a development API key is used to create an order for the customer. Basically, we mark customer objects as `production` or not simply to help distinguish development data from production data in the API and the console's user interface, however do not assume this property as immutable or even guaranteed to be true. |

## List customers

`GET /api/1/customers`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/customers
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/customers', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json",
		"Accept-Language"=>"en"
	]
]);
```

```python
import requests
endpoint = "https://api.kem.guru/api/1/customers"
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json",
	"Accept-Language":"en"
}
r = requests.get(endpoint, headers=headers)
```

> Example response

```json
[
{
  "id": "1398",
  "email": "john@example.com",
  "postcode": null,
  "name": null,
  "language": "en",
  "phone": null,
  "metadata": null
}
]
```

### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| embed  | string | Allows you to retrieve in a single call customers as well as their addresses. 														|



#### Embeds

The following objects can be embedded.

- **addresses** : A list of [addresses](#addresses) used by customers in their past orders and/or manually added with the API.


## Create customer

`POST /api/1/customers`

> Example request

```shell
curl -X POST \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	-d '{"email":"john@example.com"}' \
	https://api.kem.guru/api/1/customers
```

```php
<?php
$response = Guzzle::post('https://api.kem.guru/api/1/customers', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json",
		"Accept-Language"=>"en"
	],
    'body' => "{"email":"john@example.com"}",
]);
```

```python
import requests
endpoint = "https://api.kem.guru/api/1/customers"
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json",
	"Accept-Language":"en"
}
payload = """{"email":"john@example.com"}"""
r = requests.post(endpoint, headers=headers, data=payload)
```

> Example response

```json
{
  "id": 1234,
  "email": "john@example.com",
  "postcode": null,
  "name": null,
  "locale": {
      "id": "en_CA",
      "name": "English Canada",
      "language": "en",
      "language_name": "English"
   },
  "phone": null,
  "metadata": null
}
```

### Parameters

| Name     | Type   | Description                                                                                                      |
|----------|--------|------------------------------------------------------------------------------------------------------------------|
| email    | string | The customer's email address                                                                        |
| postcode | string | Zip/Postal Code                                                                                                  |
| name     | string | The customer's display name                                                                                      |
| phone    | string | The customer's phone number, preferably in a standardized format (such as +15145555555). If you pass a non standardized phone number, we will try to standardize it; if we are unable to convert it, we will not save it and *phone* will be `null`.                         |
| metadata | string | An optional variable you can use for any purpose relevant to your application (max of 1024 characters).          |

Note that you can set the *locale* by using the [Accept-Language](#language) header.

## Retrieve customer

`GET /api/1/customers/{id}`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/customers/1234
```

```php
<?php
$customer = base64_encode("john@example.com");
$response = Guzzle::get('https://api.kem.guru/api/1/customers/' . urlencode($customer), [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json",
		"Accept-Language"=>"en"
	]
]);
```

```python
import requests, base64, urllib
customer_email = base64.b64encode("john@example.com")
endpoint = "https://api.kem.guru/api/1/customers/" + urllib.quote_plus(customer_email)
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json",
	"Accept-Language":"en"
}
r = requests.get(endpoint, headers=headers)
```

> Example response

```json
{
  "id": 1234,
  "email": "john@example.com",
  "postcode": null,
  "name": null,
  "locale": {
      "id": "en_CA",
      "name": "English Canada",
      "language": "en",
      "language_name": "English"
   },
  "phone": null,
  "metadata": null
}
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | integer or string | The customer's object unique ID or a base 64 encoded email address. |


### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| embed  | string | Allows you to retrieve in a single call a customer object as well an array of addresses. 											|


#### Embeds

The following objects can be embedded.

- **addresses** : A list of [addresses](#addresses) used by the customer in her past orders and/or manually added with the API.


## Update customer

`PUT /api/1/customers/{id}`

> Example request

```shell
curl -X PUT \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	-d '{"email":"john@example.com", "phone":"+15145555555"}' \
	https://api.kem.guru/api/1/customers
```

```php
<?php
$customer = base64_encode("john@example.com");
$response = Guzzle::put('https://api.kem.guru/api/1/customers/' . urlencode($customer), [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json",
		"Accept-Language"=>"en"
	],
    'body' => "{"email":"john@example.com", "phone":"+15145555555"}",
]);
```

```python
import requests, base64, urllib
customer_email = base64.b64encode("john@example.com")
endpoint = "https://api.kem.guru/api/1/customers/" + urllib.quote_plus(customer_email)
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json",
	"Accept-Language":"en"
}
payload = """{"email":"john@example.com", "phone":"+15145555555"}"""
r = requests.put(endpoint, headers=headers, data=payload)
```

> Example response

```json
{
  "id": 1234,
  "email": "john@example.com",
  "postcode": null,
  "name": null,
  "locale": {
      "id": "en_CA",
      "name": "English Canada",
      "language": "en",
      "language_name": "English"
   },
  "phone": "+15145555555",
  "metadata": null
}
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | integer or string | The customer's object unique ID or a base 64 encoded email address. |

### Parameters

| Name     | Type   | Description                                                                                                      |
|----------|--------|------------------------------------------------------------------------------------------------------------------|
| email    | string | The customer's email address                                                                        |
| postcode | string | Zip/Postal Code                                                                                                  |
| name     | string | The customer's display name                                                                                      |
| phone    | string | The customer's phone number, preferably in a standardized format (such as +15145555555). If you pass a non standardized phone number, we will try to standardize it; if we are unable to convert it, we will not save it.|
| metadata | string | An optional variable you can use for any purpose relevant to your application (max of 1024 characters).          |

Note that you can set the *locale* by using the [Accept-Language](#language) header.