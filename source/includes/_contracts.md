# Contracts

## Contracts Overview

The *contract* object encapsulates actual contracts, policies, agreements and other legally binding documents that users should read and accept when using KEM Solutions services. Contracts are customized per-store, change very infrequently but still shouldn't be considered static material.

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | string  | A unique identifier for the contract.									                                        |
| title    | string  | A localized, displayable title for the contract.											                        |
| content  | string  | The contract's content, in [Markdown](https://daringfireball.net/projects/markdown/syntax)                       |
| slug     | string  | A unique, localized, SEO friendly slug that can be used in a url.                                                |


## List contracts

`GET /api/1/contracts`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	https://api.kem.guru/api/1/contracts
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/contracts', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json"
	]
]);
```

```python
import requests
endpoint = "https://api.kem.guru/api/1/contracts"
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json"
}
r = requests.get(endpoint, headers=headers)
```

> Example response

```json
[
  {
    "title": "Privacy Policy",
    "content": "## Section 1 ...",
    "id": "privacy",
    "slug": "privacy-policy"
  },
  {
    "title": "Terms and Conditions",
    "content": "This website is operated by...",
    "id": "terms",
    "slug": "terms-and-conditions"
  }
]
```

## Retrieve contract

`GET /api/1/contracts/{id}`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: 'en' \
	https://api.kem.guru/api/1/contracts/terms-and-conditions
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/contracts/terms-and-conditions', [
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
endpoint = "https://api.kem.guru/api/1/contracts/terms-and-conditions"
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
    "title": "Terms and Conditions",
    "content": "This website is operated by...",
    "id": "terms",
    "slug": "terms-and-conditions"
  }
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | string			   | The contract's ID or slug.                                          |
