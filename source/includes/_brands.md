# Brands

## Brands Overview

KEM partners can view and retrieve brands with these endpoints.

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | integer | KEM unique ID for this brand.                                                                                    |
| slug     | string  | A unique, localized, SEO friendly slug that can be used in a url											        |
| name     | string  | The brands's display name                                                                                        |

### Presentation object

When the optional `&embed` parameter includes `presentation` on `GET brands`, a presentation object, specific to the current brand, will be included in the response. Following the `presentation` object's suggestions in your views allows you to customize many brands's pages with logos, colors, images and taglines hand picked by our marketing team.

The presentation object contains the following:

| Name       | Type   | Description 										  |
|------------|--------|-------------------------------------------------------|
| title      | string | A localized headline for the brand. Might be different than the brand's name |
| subtitle   | string | A secondary headline for the brand.|
| logo	     | object | The brands' logo|
| background | object | Suggested presentation background elements for the brand.|

#### Logo

The presentation object might include a logo object, made of the following:

| Name  | Type   | Description 																			  |
|-------|--------|----------------------------------------------------------------------------------------|
| image | string | An image URL. Unlike most image urls, no path parameters are included in this url: the returned image is guaranteed to be a SVG file and therefore can be preprocessed on your end or directly served to customers in a webpage, for example. |


#### Background

The presentation object might include a background object, made of the following:

| Name  | Type   | Description 																			  |
|-------|--------|----------------------------------------------------------------------------------------|
| color | string | A suggested background color (in a CSS ready hexadecimal format, e.g. *#0F0F0F*). If an image url is passed, will be the image's dominant color. When you choose a foreground color, make sure it contrasts enough with this color to be readable. |
| image | string | A suggested background image. The url will include the following replaceable path parameters: {width}, {height} and {mode}. To know how to use these path parameter, refer to the [Product Images](#product-image-object) section of this document.                        |

## List brands

`GET /api/1/brands`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/brands
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/brands', [
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
endpoint = "https://api.kem.guru/api/1/brands"
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
    "id": 123,
    "slug": "hawtorne-wipes",
    "name": "Hawtorne Wipes"
  },
  (...)
]
```

This endpoint will return the entire brands list, ordered alphabetically.


## Retrieve brands

`GET /api/1/brands/{id}`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/brands/hawtorne-wipes
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/categories/hawtorne-wipes', [
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
endpoint = "https://api.kem.guru/api/1/categories/hawtorne-wipes"
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
  "id": 123,
  "slug": "hawtorne-wipes",
  "name": "Hawtorne Wipes"
}
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | number or string | The brands's numeric ID or slug.									 |


### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| embed  | string | Vaguely inspired by HATEOAS, embed allows you to retrieve in a single call a brand as well as products or a presentation object.    |
| filters| string | Filters applied to `products` if `&embed=products` is used.                                                                         |
| order  | string | Sorting options applied to `products`if `?embed=products` is used.                                                                  |

#### Embeds

The following objects can be embedded.

- **products** : will embed `products` in the specified brand. When products are embedded, a `promoted` array that contains a list of `products` will also be returned on page 1. These products have been manually selected by KEM's marketing team and should be displayed front and foremost. The `products` array will be [paginated](#pagination).
- **presentation** : will add an optional `presentation` object, containing styling guidelines for the current brand.

Multiple embeds can be requested by separating them with a comma (e.g. `?embed=products,presentation`).

#### Filters

The following filters can be applied to embedded `products`:

- **min_price** : only products with prices greater-than or equal to this number will be returned.
- **max_price** : only products with prices less-than or equal to this number will be returned.

Filters are cumulatives and should be separated by a comma. For example, `?embed=products&filters=min_price:10.50,max_price:11` will only return products between $10.50 and $11.

#### Order

Products can be sorted by

- **name** : alphabetically by product name
- **-name** : reverse alphatical order
- **price** : price, ascending
- **-price** : price, descending
- **added** : recency, from the newest to the oldest
- **-added** : recency, from the oldest to the newest

Only one sorting parameter is allowed (e.g. `&embed=products&order=-name`).