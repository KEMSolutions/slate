# Categories

## Categories Overview

KEM partners can view and retrieve categories with these endpoints.

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | integer | KEM unique ID for this category.                                                                                 |
| slug     | string  | A unique, localized, SEO friendly slug that can be used in a url											        |
| name     | string  | The category's display name                                                                                      |
| children | array   | An array of children categories (if applicable)																    |

### Presentation object

When the optional `&embed` parameter includes `presentation` on `GET categories`, a presentation object, specific to the current category, will be included in the response. Following the `presentation` object's suggestions in your views allows you to customize many category's pages with colors, images and taglines hand picked by our marketing team.

The presentation object contains the following:

| Name       | Type   | Description 										  |
|------------|--------|-------------------------------------------------------|
| title      | string | A localized headline for the category. Might be different than the category's title |
| subtitle   | string | A secondary headline for the category.|
| background | object | Suggested presentation background elements for the category.|

#### Background

The presentation object might include a background object, made of the following:

| Name  | Type   | Description 																			  |
|-------|--------|----------------------------------------------------------------------------------------|
| color | string | A suggested background color (in a CSS ready hexadecimal format, e.g. *#0F0F0F*). If an image url is passed, will be the image's dominant color. When you choose a foreground color, make sure it contrasts enough with this color to be readable. |
| image | string | A suggested background image. The url will include the following replaceable path parameters: {width}, {height} and {mode}. To know how to use these path parameter, refer to the [Product Images](#product-image-object) section of this document.                        |

## List categories

`GET /api/1/categories`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/categories
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/categories', [
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
endpoint = "https://api.kem.guru/api/1/categories"
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
    "id": 678,
    "slug": "categories",
    "name": "Categories",
    "children": [
      {
        "id": 987,
        "slug": "food",
        "name": "Food",
        "children": [
          {
            "id": 876,
            "slug": "abeds-chicken",
            "name": "Abed's Chicken",
            "children": []
          },
		  (...)
        ]
      },
	  (...)
    ]
  },
  (...)
]
```

This endpoint will return the entire category tree. Even though we expect the category tree to be relatively stable, it's organization could change without notice (and that includes topmost categories). Therefore, avoid hardlinking categories.


## Retrieve categories

`GET /api/1/categories/{id}`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/categories/abeds-chicken
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/categories/abeds-chicken', [
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
endpoint = "https://api.kem.guru/api/1/categories/abeds-chicken"
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
	"id": 876,
	"slug": "abeds-chicken",
	"name": "Abed's Chicken",
	"children": []
}
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | number or string | The category's numeric ID or slug.									 |


### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| embed  | string | Vaguely inspired by HATEOAS, embed allows you to retrieve in a single call a category as well as products or a presentation object. |
| filters| string | Filters applied to `products` if `&embed=products` is used.                                                                         |
| order  | string | Sorting options applied to `products`if `?embed=products` is used.                                                                  |

#### Embeds

The following objects can be embedded.

- **products** : will embed `products` in the specified category. When products are embedded, a `promoted` array that contains a list of `products` will also be returned on page 1. These products have been manually selected by KEM's marketing team and should be displayed front and foremost. The `products` array will be [paginated](#pagination).
- **presentation** : will add an optional `presentation` object, containing styling guidelines for the current category.

Multiple embeds can be requested by separating them with a comma (e.g. `?embed=products,presentation`).

#### Filters

The following filters can be applied to embedded `products`:

- **min_price** : only products with prices greater-than or equal to this number will be returned.
- **max_price** : only products with prices less-than or equal to this number will be returned.
- **brands** : only products from the specified brands will be returned. Multiple brand IDs can be specified, separated by a semicolon (e.g. `brands:123;321`).

Filters are cumulatives and should be separated by a comma. For example, `?embed=products&filters=min_price:10.50,max_price:11,brands:123;321` will only return products between $10.50 and $11 from the brands 123 and 321.

#### Order

Products can be sorted by

- **name** : alphabetically by product name
- **-name** : reverse alphatical order
- **price** : price, ascending
- **-price** : price, descending
- **added** : recency, from the newest to the oldest
- **-added** : recency, from the oldest to the newest

Only one sorting parameter is allowed (e.g. `&embed=products&order=-name`).