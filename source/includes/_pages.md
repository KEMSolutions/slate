# Pages

## Pages Overview

The *page* object encapsulates custom content your application should be able to present to users. Pages allow users with little to no technical knowledge to create custom content in their respective store using an easy to use CMS like user interface.

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | integer | A unique identifier for the page.									                                        |
| title    | string  | A localized, displayable title for the page.											                        |
| content  | string  | The pages content. |
| type     | string  | A string identifying the content type. Can be `markdown` or `html`. |
| slug     | string  | A unique, localized, SEO friendly slug that can be used in a url.                                                |
| visible  | boolean | If `true`, the page should be displayed. When set to `false`, the page's content might still be in a draft state. |


## List pages

`GET /api/1/pages`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	https://api.kem.guru/api/1/pages
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/pages', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json"
	]
]);
```

```python
import requests
endpoint = "https://api.kem.guru/api/1/pages"
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
    "id": 7654,
    "slug": "troy-and-abed-schedule",
    "title": "Troy and Abed  - Schedule",
    "content": "# Hello, world!",
    "type": "markdown",
    "visible": true
  }
]
```

## Retrieve page

`GET /api/1/contracts/{id}`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: 'en' \
	https://api.kem.guru/api/1/pages/troy-and-abed-schedule
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/pages/troy-and-abed-schedule', [
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
endpoint = "https://api.kem.guru/api/1/pages/troy-and-abed-schedule"
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
    "id": 7654,
    "slug": "troy-and-abed-schedule",
    "title": "Troy and Abed  - Schedule",
    "content": "# Hello, world!",
    "type": "markdown",
    "visible": true
  }
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | string			   | The page's ID or slug.                                              |
