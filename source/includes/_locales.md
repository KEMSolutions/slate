# Locales

## Locales Overview

Locales encapsulate information about linguistic, cultural, and technological conventions and standards. In the context of the KEM API, locales are objects containing basic information about a language supported by the KEM Console. Your application must support one or more of these locales, and pass it's `id` or `language` value in a `Accept-Language:` header (see [Language](#language)).

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | string  | The complete locale identifier, including a regional "flavor"                                     |
| name     | string  | The locale's full display name.											                        |
| language | string  | The language  ISO 639-1 (two letters) identifier                                                                 |
| language_name | string  | The language's full display name                                                                 			|
| script   | string  | The four letter identifier for the locale's writing system. Conforms to [ISO 15924](https://en.wikipedia.org/wiki/ISO_15924) codes.|

## List locales

`GET /api/1/locales`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	https://api.kem.guru/api/1/locales
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/locales', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json"
	]
]);
```

```python
import requests
endpoint = "https://api.kem.guru/api/1/locales"
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
      "id": "en_CA",
      "name": "English Canada",
      "language": "en",
      "language_name": "English"
    },
  (...)
]
```

This endpoint will return the entire locale list. Note that all the locales are accepted by the KEM API but the API will not necessarely return the same content for each locales. For example, a product available in Canadian French (*fr-CA*) and Canadian English (*en-CA*) might not be available in Greek (*il*). However, if Greek is listed in `GET /locales`, you can freely use `Accept-Language: il` in any API transaction, without raising an exception.

This list of locales can also be different than the list returned on [`GET /store`](#store) since `GET /locales` returns all acceptables locales and not just a curated list of selected locales your store is expected to be able to handle.

## Retrieve locale

`GET /api/1/locales/{id}`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: 'en' \
	https://api.kem.guru/api/1/locales/en_CA
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/locales/en_CA', [
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
endpoint = "https://api.kem.guru/api/1/locales/en_CA"
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
	"id": "en_CA",
	"name": "English Canada",
	"language": "en",
	"language_name": "English"
}
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | string			   | The locales's ID.													 |
