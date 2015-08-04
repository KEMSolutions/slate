# Addresses

## Addresses Overview

Addresses are physical locations. KEM uses them to ship to actual physical locations and bill customers. Since *addresses* are linked to [customers](#customers), you can easily allow users to select and re-use their address for subsequent orders.


| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | integer | KEM unique ID for this address.                                                                                  |
| line1    | string  | The fist line of an address.																						|
| line2    | string  | The optional second line of an address.                                                                          |
| name     | string  | The customer's display name                                                                                      |
| postcode | string  | A postal code or ZIP code. Mandatory for Canadian and US addresses, optional anywhere else.						|
| city     | string  | The city's name.																									|
| province | string  | A two letter province or state  postal abbreviation for Canadian ([ISO 3166-2:CA](https://en.wikipedia.org/wiki/ISO_3166-2:CA)) or US ([ISO_3166-2:US](https://en.wikipedia.org/wiki/ISO_3166-2:US)) addresses. Optional (two letters only) for other countries.|
|country   | string  | A two letter country code ([ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)). |
|phone     | string  | An optional phone number, preferably in a standardized format (such as +15145555555). |
|customer  | object  | The address's owner, a [customer](#customers) object. |

## List addresses

`GET /api/1/addresses`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/addresses
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/addresses', [
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
endpoint = "https://api.kem.guru/api/1/addresses"
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
		"id": 987,
		"line1": "37 Greendale Avenue",
		"line2": "Apt 354",
		"name": "Troy Barnes",
		"postcode": "H2V 4G7",
		"city": "Montreal",
		"province": "QC",
		"country": "CA",
		"phone": "+15143035667",
		"customer": {
			"id": 7654
		}
	},
	(...)
]
```

### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| embed  | string | Allows you to retrieve in a single call all address as well the associated customers. 												|



#### Embeds

The following objects can be embedded for each *address* object.

- **customer** : A [customer](#customers) object associated with the address.


## Create an address

`POST /api/1/addresses`

> Example request

```shell
curl -X POST \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	-d '{"line1": "37 Greendale Avenue", \
		"line2": "Apt 354", \
		"name": "Troy Barnes", \
		"postcode": "H2V 4G7", \
		"city": "Montreal", \
		"province": "QC", \
		"country": "CA", \
		"phone": "+15143035667", \
		"customer": { \
			"id": 7654 \
		} \
	}' \
	https://api.kem.guru/api/1/addresses
```

```php
<?php
$body = <<<EOT
{
	"line1": "37 Greendale Avenue",
	"line2": "Apt 354",
	"name": "Troy Barnes",
	"postcode": "H2V 4G7",
	"city": "Montreal",
	"province": "QC",
	"country": "CA",
	"phone": "+15143035667",
	"customer": {
		"id": 7654
	}
}
EOT;

$response = Guzzle::post('https://api.kem.guru/api/1/addresses', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json",
		"Accept-Language"=>"en"
	],
    'body' => $body,
]);
```

```python
import requests
endpoint = "https://api.kem.guru/api/1/addresses"
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json",
	"Accept-Language":"en"
}
payload = """
{
	"line1": "37 Greendale Avenue",
	"line2": "Apt 354",
	"name": "Troy Barnes",
	"postcode": "H2V 4G7",
	"city": "Montreal",
	"province": "QC",
	"country": "CA",
	"phone": "+15143035667",
	"customer": {
		"id": 7654
	}
}
"""
r = requests.post(endpoint, headers=headers, data=payload)
```

> Example response

```json
{
	"id": 987,
	"line1": "37 Greendale Avenue",
	"line2": "Apt 354",
	"name": "Troy Barnes",
	"postcode": "H2V 4G7",
	"city": "Montreal",
	"province": "QC",
	"country": "CA",
	"phone": "+15143035667",
	"customer": {
		"id": 7654
	}
}
```


## Retrieve an address

`GET /api/1/addresses/{id}`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/addresses/987
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/addresses/987', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json",
		"Accept-Language"=>"en"
	]
]);
```

```python
endpoint = "https://api.kem.guru/api/1/customers/987"
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
	"id": 987,
	"line1": "37 Greendale Avenue",
	"line2": "Apt 354",
	"name": "Troy Barnes",
	"postcode": "H2V 4G7",
	"city": "Montreal",
	"province": "QC",
	"country": "CA",
	"phone": "+15143035667",
	"customer": {
		"id": 7654
	}
}
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | integer | The address' object unique ID.									 	 |


### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| embed  | string | Allows you to retrieve in a single call an address as well the associated customer. 												|



#### Embeds

The following objects can be embedded.

- **customer** : A [customer](#customers) object associated with the address.


## Update an address

`PUT /api/1/addresses/{id}`

> Example request

```shell
curl -X PUT \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	-d '{"line1": "37 Greendale Avenue", \
		"line2": "Apt 354", \
		"name": "Troy Barnes", \
		"postcode": "H2V 4G7", \
		"city": "Montreal", \
		"province": "QC", \
		"country": "CA", \
		"phone": "+15143035667", \
		"customer": { \
			"id": 7654 \
		} \
	}' \
	https://api.kem.guru/api/1/addresses/987
```

```php
<?php

$body = <<<EOT
{
	"line1": "37 Greendale Avenue",
	"line2": "Apt 354",
	"name": "Troy Barnes",
	"postcode": "H2V 4G7",
	"city": "Montreal",
	"province": "QC",
	"country": "CA",
	"phone": "+15143035667",
	"customer": {
		"id": 7654
	}
}
EOT;

$response = Guzzle::put('https://api.kem.guru/api/1/addresses/987', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json",
		"Accept-Language"=>"en"
	],
    'body' => $body,
]);
```

```python

endpoint = "https://api.kem.guru/api/1/customers/987"
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json",
	"Accept-Language":"en"
}
payload = """{
	"line1": "37 Greendale Avenue",
	"line2": "Apt 354",
	"name": "Troy Barnes",
	"postcode": "H2V 4G7",
	"city": "Montreal",
	"province": "QC",
	"country": "CA",
	"phone": "+15143035667",
	"customer": {
		"id": 7654
	}
}"""
r = requests.put(endpoint, headers=headers, data=payload)
```

> Example response

```json
{
	"id": 987,
	"line1": "37 Greendale Avenue",
	"line2": "Apt 354",
	"name": "Troy Barnes",
	"postcode": "H2V 4G7",
	"city": "Montreal",
	"province": "QC",
	"country": "CA",
	"phone": "+15143035667",
	"customer": {
		"id": 7654
	}
}
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | integer | The customer's object unique ID.												 |

