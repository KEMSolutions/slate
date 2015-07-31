# Store

## Store Overview

The store object encapsulates basic information about your store. Many variables in the store object can be easily modified by *non-technical* users from the KEM Console user interface. Instead of hardcoding these values in your code, we recommand you fetch them once and cache them, if possible.

<aside class="notice">It is not currently possible to edit these values directly from the API, but a PUT request will eventually allow you to do so.</aside>

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| id       | string  | The store ID.															                                        |
| name     | string  | The store's full display name.               											                        |
| language | string  | The store's URL (can be null in the case of a middleware or a native mobile app)                                 |
| logo     | object  | The store's logo object				                                                                 			|
| support  | object  | The store's support object				                                                                 		|
| colors   | object  | The store's colors object				                                                                 		|


### Logo object

The logo object contains an image url as well as it's type. We always ask for a high resolution, transparent, visible over a light background, square PNG when we open a new store. However, users can update their store's logo manually from the KEM Console and, even though we enforce a minimum image size and the PNG file format, we have no way to actually make sure the uploaded image has a transparent background or will be visible over a light background.

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| type     | string  | The image's MIME type.															                                |
| url      | string  | The logo's url. The url will include the following replaceable path parameters: {width}, {height} and {mode}. To know how to use these path parameter, refer to the [Product Images](#product-image-object) section of this document.													|

### Support object

The support object help you customize your customer's experience by displaying the store's custom support email and phone number.

| Name     | Type    | Description                                                                                                      |
|----------|---------|------------------------------------------------------------------------------------------------------------------|
| email    | string  | The store's customer service email address.															            |
| phone    | string  | The store's customer service phone number. Will be returned in a normalized phone format (e.g. *+15145551234*).  |

### Colors object

The colors object help you customize your store's appearance by allowing users to update a color palette or provide css that can be injected in your website's pages for further customization if your application renders using web technologies.

All colors are served in a standard hex triplet format (where *FF0000* is pitch red). The following indications describe how they are used in a stock Boukem store. Of course, as their name indicate, they are to be considered entirely app agnostic and you can use them as you'd like. Using the `PUT store` you can update the KEM Console color picker to reflect how these colors are used by your application.

| Name         | Type    | Description                                                                                                      |
|--------------|---------|------------------------------------------------------------------------------------------------------------------|
| color_one    | string  | (Dark) The store's primary color, used for main call to actions.       				                            |
| color_two    | string  | (Dark) A primary color complement, used for secondary call to actions.                            			   |
| color_three  | string  | (Light) A neutral, background color.                                        									  |
| color_four   | string  | (Dark) A color that contrasts with `color_three`. Normally used as the footer's background color.			    |
| color_five   | string  | (Dark) Accent color, that will be used sparingly, contrasting with `color_one`. 									|
| css          | string  | A CSS string that can be safely injected after your applications' style sheet. Mostly useful if you decide to use our stock Boukem store and only customize it using the Console's user interface. |


## Retrieve store

`GET /api/1/store`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	https://api.kem.guru/api/1/store?embed=locales,contracts
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/store?embed=locales,contracts', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json"
	]
]);
```

```python
import requests
endpoint = "https://api.kem.guru/api/1/store?embed=locales,contracts"
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json"
}
r = requests.get(endpoint, headers=headers)
```

> Example response

```json
{
  "id": 10,
  "name": "Greendale souvenirs",
  "url": "http:\\/\\/greendale.edu.example.com",
  "logo": {
    "type": "image\\/png",
    "url": "https:\\/\\/img.kem.guru\\/logo-10-{width}-{height}-{mode}.png"
  },
  "support": {
    "email": "support@greendale.edu.example.com",
    "phone": null
  },
  "colors": {
    "color_one": "d6282d",
    "color_two": "2b5f8c",
    "color_three": "f2f2f2",
    "color_four": "f2dc9b",
    "color_five": "8bd9bc",
    "css": ".custom_element {font-size:5em;}"
  },
  "locales": [
    {
      "id": "en-CA",
      "name": "English Canada",
      "language": "en",
      "language_name": "English"
    },
    (...)
  ],
  "contracts": [
  {
    "title": "Privacy Policy",
    "content": "## Section 1 ...",
    "id": "privacy",
    "slug": "privacy-policy"
  },
  (...)
]
}
```

### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|------------------------------------------------------------------------------------------------------------------------|
| embed  | string | You can use the embed parameter to retrieve, in a single call, all the necessary objects your store needs for initialization. |

#### Embeds

The following objects can be embedded using the following keywords:

- **contracts** : will embed the list of applicable [contracts](#contracts) your store must present to your users.
- **locales** : will embed the list of [locales](#locales) your store is expected to be able to handle. This list is different from the broader `GET locales` as the returned locales were manually selected for your store in the KEM Console.
- **pages** : will embed the list of applicable [pages](#pages) your store must present to your users.

Multiple objects can be embeded by separating keywords with a comma.