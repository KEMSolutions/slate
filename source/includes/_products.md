# Products

## Products Overview

The *product* object is central to KEM API as it provides everything there is to know about a given product.

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | integer | KEM unique ID for this product.                                                                                  |
| taxable  | boolean | Does tax normally apply to this product.																			|
| enabled  | boolean | Should the product be visible and available to buy by customers. Will normaly be *true* but might be *false* when retrieving older orders' content. Do not present a disabled product's description or photos to customers. 							   |
| slug   | string   | A url-friendly slug. Can be used to retrieve a product in place of a product ID. |
| rating | float    | Product score, on a 0 to 1 scale. 					 														   |
| brand  | object   | A [brand](#brands) object.																						|
| localization  | object   | The product's localization object. 																		|
| images | array | An array of product images.																						    |
| formats | array | A list of format objects.																						    |


### Localization object

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| slug     | string  | A url-friendly slug. Can be used to retrieve a product in place of a product ID. 								|
| name     | string  | The product's name.																								|
| format   | string  | Localized product format (if available).																			|
| short_description   | string  | A plain text product description. Data entry clerks are instructed to keep these short descriptions around 140 characters long.|
| long_description   | string  | A [markdown](https://daringfireball.net/projects/markdown/syntax) formatted product description.		|
| custom_description | object | A custom description object. 																			|

#### Custom description object

Custom descriptions are used on stores to write a personalized recommandation.

![Example of a custom description](images/products_overview_localization_custom_description.png)

| Name     | Type      | Description                                                                                                    |
|----------|-----------|----------------------------------------------------------------------------------------------------------------|
| content  | string    | [Markdown](https://daringfireball.net/projects/markdown/syntax) formatted recommendation content.              |
| author_name| string  | The name of the person or organization issuing the recommendation or endorsing the product.                    |
| author_avatar| string| *Deprecated* A base64 encoded PNG avatar for the author. Images returned are always square 500x500 pngs.       |

### Product image object

Product images are provided as an array where the first image is the main product image.

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | integer | The product image ID.																			                |
| url	   | string  | A product URL. The url will include the following replaceable path parameters: {width}, {height} and {mode}.     |

#### Path parameters

Image URLs include replaceable path parameters that must be substituted for actual values.

- **{width}** : The desired image width, expressed in pixels.
- **{height}** : The desired image height, expressed in pixels.
- **{mode}** : A comma separated list of filters, or an empty string if no filter should be applied. The following filters are accepted:
	- **blowup** : Used in conjonction with the `fit` mode to guarantee an image's size, even if that means scaling it up.
	- **blur:amount** : Apply a gaussian blur filter with a optional amount on the current image. Use values between 0 and 100.
	- **brightness:amount** : Changes the brightness of the current image by the given level. Use values between -100 for min. brightness 0 for no change and +100 for max. brightness.
	- **contrast:amount** : Changes the contrast of the current image by the given level. Use values between -100 for min. contrast 0 for no change and +100 for max. contrast.
	- **fit** : Combine cropping and resizing to format image in a smart way. The method will find the best fitting aspect ratio of your given width and height on the current image automatically, cut it out and resize it to the given dimension.
	- **gamma:amount** : Performs a gamma correction operation on the current image (eg.: `gamma:1.6`).
	- **greyscale** Apply a greyscale filter.
	- **mask:type** : Apply a given shape as alpha mask to the current image to change current opacity. Any transparency values of the current image will be maintained. *circle*, *superellipse* and *roundedrect*.


### Format object

A product can be available in different formats. For example, a water bottle could be available for sale in a 500 ml format as well as a 750 ml, each format at different price point.

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | string  | KEM ID for this format. Is unique among formats of the same product but not globally.							|
| name     | string  | A localized, displayable name for the format.																	|
| price    | float   | Regular sale price.																						        |
| barcode  | string  | A string containing a 12 digits UPC code or a 13 digits EAN symbol, exactly as a barcode scanner would read it on the product's label. |
| sku      | string  | *Deprecated* A supplier or manufacturer specific sku. Data accuracy can vary greatly.							|
| weight   | float   | The products weight in kilograms.																				|
| discontinued| boolean  | When discontinued is *true*, a product format is not available for purchase and should not be sold. Unlike disabled products, discontinued products were not removed from sale because of a quality issue but mostly because of product shortages. Discontinued products might even come back on the market at some point in the future. If your application is publicly accessible on the web, you can avoid losing search engine placement for discontinued products by presenting discontinued products pages to customers while disabling their purchase buttons. Be aware that a single format can be discontinued while others are not. |
| reduced_price| object| When a product is on sale, a *Reduced price object will be included. Defaults to *null* normally. 			   |
| inventory | object | An inventory object.																						    |



#### Inventory object

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| count    | integer | A count of currently available product in stock. Products not in stock (`count` of 0) can still be bought if they are not discontinued nor disabled. |
| availability | integer | Days count before the product will be shipped (approximatively). |


#### Reduced price object

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| float    | price   | The product's format reduced price.                                                                              |



## Search products

`GET /api/1/products/search`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/products/search?q=troy+abed+mug
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/products/search?q=troy+abed+mug', [
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
endpoint = "https://api.kem.guru/api/1/products/search?q=troy+abed+mug"
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
  "organic_results": [
   {
     "id": 123,
     "taxable": true,
     "enabled": true,
     "formats": [
	 	{
			"id": "32-wipes",
			"name": "32 wipes",
			"price": 19.95,
			"barcode": "123456789012",
	        "sku": "B003SXGN7K",
	        "weight": 0.23,
			"discontinued": false,
	        "reduced_price": {
	      	  "price": 15.95
	        },
	        "inventory": {
	          "count": 0
	        }
		}
	 ],
     "rating": 0.98,
     "brand": {
       "id": 123,
       "slug": "hawtorne-wipes",
       "name": "Hawtorne Wipes"
     },
     "localization": {
       "name": "Troy and Abed Mug",
       "short_description": "Excellent mug for a fake morning show.",
       "long_description": "An interview with Señor Chang...",
       "slug": "troy-abed-morning-mug",
       "custom_description": {
         "content": "I use this mug every single day...",
         "author_name": "Dean Pelton",
         "author_avatar": "{base64 encoded png}"
       }
     },
     "images": [
       {
         "id": 9876,
         "url": "https:\\/\\/img.kem.guru\\/product-8-8915-troy-abed-morning-mug-{width}-{height}-{mode}"
       }
     ]
   }
  ],
  "tags": [
    {
      "name": "Hawtorn Wipes",
      "id": 1,
      "products": [
	    {
	      "id": 123,
	      "taxable": true,
	      "enabled": true,
	      "formats": [
	 	 	{
				"id": "32-wipes",
				"name": "32 wipes",
	 			"price": 19.95,
	 			"barcode": "123456789012",
	 	        "sku": "B003SXGN7K",
	 	        "weight": 0.23,
	 			"discontinued": false,
	 	        "reduced_price": {
	 	      	  "price": 15.95
	 	        },
	 	        "inventory": {
	 	          "count": 0
	 	        }
	 		}
	 	 ],
	      "rating": 0.98,
	      "brand": {
	        "id": 123,
	        "slug": "hawtorne-wipes",
	        "name": "Hawtorne Wipes"
	      },
	      "localization": {
	        "name": "Troy and Abed Mug",
	        "short_description": "Excellent mug for a fake morning show.",
	        "long_description": "An interview with Señor Chang...",
	        "slug": "troy-abed-morning-mug",
	        "custom_description": {
	          "content": "I use this mug every single day...",
	          "author_name": "Dean Pelton",
	          "author_avatar": "{base64 encoded png}"
	        }
	      },
	      "images": [
	        {
	          "id": 9876,
	          "url": "https:\\/\\/img.kem.guru\\/product-8-8915-troy-abed-morning-mug-{width}-{height}-{mode}"
	        }
	      ]
	    }
      ]
    }
  ]
}

```

### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| embed  | string | Can be used to include contextually picked secondary object type.																	|
| filters| string | Filters applied to `organic_results`.                             					                                                |
| order  | string | Sorting options applied to `organic_results`					                                                                    |
| q      | string | A query string that will be used to find corresponding search results.                                                              |

#### Embeds

The following objects can be embedded.

- **tags** : will use the search query to retrieve by name an optional `tag` object (if any exists for that name and locale). Tags are keywords with an associated array of hand picked products that can be suggested as highly relevant products. Will only be included on `page=1`.

#### Filters

The following filters can be applied to `organic_results`:

- **min_price** : only products with prices greater-than or equal to this number will be returned.
- **max_price** : only products with prices less-than or equal to this number will be returned.
- **brands** : only products from the specified brands will be returned. Multiple brand IDs can be specified, separated by a semicolon (e.g. `brands:123;321`).
- **categories** : only products from the specified categories will be returned. Multiple category IDs can be specified, separated by a semicolon (e.g. `categories:123;321`).

Filters are cumulatives and should be separated by a comma. For example, `?q=Hawtorne+wipes&filters=min_price:10.50,max_price:11,brands:123;321,categories:555;777` will only return products between $10.50 and $11 from the brands 123 or 321 and that are in the categories 555 or 777.

#### Order

Products can be sorted by

- **price** : price, ascending
- **-price** : price, descending
- **relevance** : relevance, from the least relevant to the most relevant
- **-relevance** : recency, from the most relevant to the least relevant (*default*).

Only one sorting parameter is allowed (e.g. `&q=Hawtorne+wipes&order=-price`).

### Response

| Name            | Type  | Description                      |
|-----------------|-------|----------------------------------|
| organic_results | array | A paginated array of `products`. |


## Retrieve products

`GET /api/1/products/{id}`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/products/troy+and+abed+morning+mug
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/categories/troy+and+abed+morning+mug', [
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
endpoint = "https://api.kem.guru/api/1/categories/troy+and+abed+morning+mug"
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
     "taxable": true,
     "enabled": true,
     "formats": [
	 	{
			"id": "32-wipes",
			"name": "32 wipes",
			"price": 19.95,
			"barcode": "123456789012",
	        "sku": "B003SXGN7K",
	        "weight": 0.23,
			"discontinued": false,
	        "reduced_price": {
	      	  "price": 15.95
	        },
	        "inventory": {
	          "count": 0
	        }
		},
	 	{
			"id": "64-wipes",
			"name": "64 wipes",
			"price": 29.95,
			"barcode": "123456789013",
	        "sku": "B003SXGN7Z",
	        "weight": 0.46,
			"discontinued": false,
	        "reduced_price": {
	      	  "price": 24.99
	        },
	        "inventory": {
	          "count": 16
	        }
		}
	 ],
     "rating": 0.98,
     "brand": {
       "id": 123,
       "slug": "hawtorne-wipes",
       "name": "Hawtorne Wipes"
     },
     "localization": {
       "name": "Troy and Abed Mug",
       "short_description": "Excellent mug for a fake morning show.",
       "long_description": "An interview with Señor Chang...",
       "slug": "troy-abed-morning-mug",
       "custom_description": {
         "content": "I use this mug every single day...",
         "author_name": "Dean Pelton",
         "author_avatar": "{base64 encoded png}"
       }
     },
     "images": [
       {
         "id": 9876,
         "url": "https:\\/\\/img.kem.guru\\/product-8-8915-troy-abed-morning-mug-{width}-{height}-{mode}"
       }
     ]
   }
```

### Path parameters

| Name | Type              | Description                                                         |
|------|-------------------|---------------------------------------------------------------------|
| {id} | number or string | The product's numeric ID or slug.									 |


### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|-----------------------------------------------------------------|
| embed  | string | Used to retrieve everything about this product in a single call |

#### Embeds

The following objects can be embedded.

- **categories** : will embed all the specific `categories` for the specified product.
- **videos** : will embed all product videos for the current product.


Multiple embeds can be requested by separating them with a comma (e.g. `?embed=products,presentation`).