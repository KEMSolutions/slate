# Orders

## Orders Overview

The *order* object is central to KEM fulfillment services. Creating an order might be somewhat of a complex operation, particularly if you are implementing a cart-based user interface in your application. When your implementing the ordering workflow, take a look to our [code samples](https://github.com/kemsolutions).

![Example of an order workflow](images/orders_overview_example_workflow.svg)

Above, a sample workflow illustrating the interactions between your application and the KEM API. 

The *order* object itself is a relatively straightforward object.

|       Name       |   Type  |                                                                                                                   Description                                                                                                                   |
|:----------------:|:-------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| id               | integer | KEM unique ID for this order. Customers will need this number to request assistance for their order.                                                                                                                                            |
| production       | boolean | When `production` is false, the order was created with a development API key and should be treated as a test order. Unlike [customers](#customers), orders `production` flag is immutable; a `production : true` order will never be fulfilled. |
| date             | string  | The order creation date, in an ISO 8601 compatible format (e.g. "1970-10-28 13:22:14").                                                                                                                                                         |
| status           | string  | The order status.                                                                                                                                                                                                                               |
| shipping_address | object  | The order's delivery [address](#address). Can be exceptionally `null` for order created over the phone.                                                                                                                                         |
| billing_address  | object  | The order's billing [address](#address). If `billing_address` is null while `shipping_address` is not, `shipping_address` is also the `billing_address`.                                                                                        |
| verification     | integer | A four digits code used to verify the customers's ownership of the order. We currently use this PIN to identify customers who want to pick up their order at our warehouse, but we could use it elsewhere.                                      |
| payment_details  | object  | Order payment information.                                                                                                                                                                                                                      |
| items            | array   | An array of Items objects.                                                                                                                                                                                                                      |

The `status` variable can take the following values:

- **paid** : the order was paid and is awaiting fulfillment by KEM.
- **pending** : the order was created but is awaiting payment. You should redirect the user to the payment url.
- **canceled** : the entire order has been canceled by KEM.
- **complete** : the order has been paid and shipped to the end user.
- **partial_shipment** : one or more items from the order have been shipped, but one or more items in the order are still awaiting shipment.

### Payment details object

The payment details object encompasses the whole financial aspects of the transaction. Feel free to use it as a reference and display these values to the customer.

|     Name    |  Type  |                                                                 Description                                                                 |
|:-----------:|:------:|:-------------------------------------------------------------------------------------------------------------------------------------------:|
| total       | float  | The total monetary value of the order, including all fees.                                                                                  |
| subtotal    | float  | The total monetary value of all the items in the order's `items` array.                                                                     |
| taxes       | float  | The total amount of taxes paid by the user.                                                                                                 |
| shipping    | float  | The order's shipping fees.                                                                                                                  |
| currency    | string | The currency of `total`, `subtotal`, `taxes`, `balance` and `shipping` are displayed in. Always *CAD*.                                      |
| balance     | float  | The remaining amount to pay on the order. Can be a negative number if the order has been canceled and the refund hasn't been yet processed. |
| payment_url | string | A URL users should be redirect to to pay for their order if the order status is *pending*.                                                  |

### Item object

An array of items is included with the order. Each of these items are pointers to specific [product](#products) with some order-specific data. To reduce the complexity of the order, the `item` object only contains the product's id, unless a `&embed=products` parameter is included.

|   Name   |   Type  |                                                                               Description                                                                              |
|:--------:|:-------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| quantity | integer | How much of the product has been ordered.                                                                                                                              |
| price    | float   | The price paid for a single item. In some cases (e.g. when a product bundle or some other discount is applied) this price can greatly differ from the product's price. |
| canceled | boolean | When true, the specified item has been canceled and will be refunded or exchanged.                                                                                     |
| shipped  | boolean | When `true`, the item has been shipped.                                                                                                                                |
| id       | integer | The item's [product](#products) ID.                                                                                                                                    |
| product  | object  | If `&embed=products` is specified, a `product` variable is added to the object, containing a localized [*product*](#products) object.                                  |


#### Shipping object

The shipping object contains the basic information needed to let users choose a shipping method. Shipping objects are retrieved in the *estimate* call and passed back to the API in the *order create* call. A shipping estimate is valid for 3600 seconds from the time it is retrieved, as the shipping service objects are dated and signed. When a shipping signature expires, passing the shipping service object to the *order create* API call might result in a slightly different total price.

|   Name   |   Type  |                                                                               Description                                                                              |
|:--------:|:-------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| method   | string  | The shipping method identifier.                                                                                                                                        |
| name     | string  | A localized and displayable maketing shipping method name.																											  |
| signature| string  | A signature to guarantee the estimate for 3600 seconds.                                                                                     							  |
| price    | float   | The value of this shipping option.                                                                                                                                     |
| delivery | string  | The roughly estimated delivery date. Might be null if the carrier is unable to provide it.																			  |
| transit  | integer | The estimated transit time; unline `delivery`, does not include any package handling time.	 																		  |
| taxes    | array   | An array of applicable taxes to the shipping method. 																												  |

#### Taxes object

A tax object contains data about an applicable tax. Taxes objects generally only apply to orders that are to be shipped to Canadian provinces.

| Name     | Type   | Description                                                                                                      |
|----------|--------|------------------------------------------------------------------------------------------------------------------|
| rate     | float  | The tax rate, in percent.			                                                                               |
| name     | string | A localized, displayable name.  																				   |
| amount   | float  | The tax applicable price.		  																				   |


## Estimate taxes and shipping

`POST /api/1/orders/estimate`

> Example request

```shell
curl -X POST \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	-d '{"products":[{"id":4321,"quantity":1},{"id":1234,"quantity":2}],"shipping_address":{"postcode":"H2V 4G7","country":"CA"}}' \
	https://api.kem.guru/api/1/orders/estimate
```

```php
<?php
$response = Guzzle::post('https://api.kem.guru/api/1/orders/estimate', [
    'headers' => [
		"X-Kem-User"=>"",
		"X-Kem-Signature"=>"",
		"Content-Type"=>"application/json",
		"Accept-Language"=>"en"
	],
	'body' => '{"products":[{"id":4321,"quantity":1},{"id":1234,"quantity":2}],"shipping_address":{"postcode":"H2V 4G7","country":"CA"}}',
]);
```

```python
import requests
endpoint = "https://api.kem.guru/api/1/orders/estimate"
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json",
	"Accept-Language":"en"
}
payload = """{"products":[{"id":4321,"quantity":1},{"id":1234,"quantity":2}],"shipping_address":{"postcode":"H2V 4G7","country":"CA"}}"""
r = requests.post(endpoint, headers=headers, data=payload)
```

> Example response

```json

{
  "shipping": {
    "services": [
      {
        "method": "DOM.EP",
        "name": "Canada Post | Expedited Parcel",
        "signature": "1438032153:1xQk1n3Bn1W0:875ce41b0f8f60ba4feb485fe4dd2ded94d41fb632efbdfdffc0b16d27301d18",
        "price": 7.95,
        "delivery": "2015-07-28",
        "transit": 1,
        "taxes": [
          {
            "rate": 9.975,
            "name": "TVQ",
            "amount": 0.79
          },
          {
            "rate": 5,
            "name": "TPS",
            "amount": 0.4
          }
        ]
      }
    ],
    "error": null
  },
  "taxes": [
    {
      "rate": 9.975,
      "name": "TVQ",
      "amount": 14.14
    },
    {
      "rate": 5,
      "name": "TPS",
      "amount": 7.09
    }
  ]
}

```

Before creating an order, it is highly recommended to get a taxes and shipping estimate to retrieve a list of available shipping options, as well as displaying the appropriate total information to the customer.


### Parameters

| Name     | Type   | Description                                                                                                      |
|----------|--------|------------------------------------------------------------------------------------------------------------------|
| products | array  | An array of item objects.                                                                                        |
| shipping_address | object | An address object stub that only contains a `country` variable as well as a `postcode` when the order is shipping to Canada. |

### Response

| Name     | Type   | Description                                                                                                      |
|----------|--------|------------------------------------------------------------------------------------------------------------------|
| shipping | object | An object that contains a `services` array of shipping objects, as well as an `error` localized and displayed string that can be presented to customers when the request fails.|
| taxes    | array  | An array of taxes objects applicable to the order's subtotal (shipping excluded). |

### Order total

To display an accurate order total, you should add to the order's subtotal all taxes as well as the select shipping method and it's own taxes objects. For example:

![The order total algorithm of a sample order](images/orders_overview_total_algorithm.svg)


## List orders


`GET /api/1/orders`

> Example request

```shell
curl -X GET \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	https://api.kem.guru/api/1/orders
```

```php
<?php
$response = Guzzle::get('https://api.kem.guru/api/1/orders', [
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
endpoint = "https://api.kem.guru/api/1/orders"
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
    "payment_details": {
      "total": 51.54,
      "subtotal": 36.95,
      "taxes": 6.71,
      "shipping": 7.88,
      "currency": "CAD",
      "payment_url": "https:\\/\\/pay.kem.guru\\/order\\/54321\\/8476",
      "balance": 51.54
    },
    "status": "pending",
    "shipping_address": {
      "line1": "37 Greendale Avenue",
      "line2": "Apt 354",
      "name": "Troy Barnes",
      "postcode": "H2V 4G7",
      "city": "Montreal",
      "province": "QC",
      "country": "CA",
      "phone": "+15143035667"
    },
    "shipping_address": {
      "line1": "37 Greendale Avenue",
      "line2": "Apt 354",
      "name": "Troy Barnes",
      "postcode": "H2V 4G7",
      "city": "Montreal",
      "province": "QC",
      "country": "CA",
      "phone": "+15143035667"
    },
    "id": 54321,
    "production": true,
    "verification": 8476,
    "date": "2015-06-19 16:15:49",
    "items": [
      {
        "quantity": 1,
        "price": 36.95,
        "canceled": false,
        "shipped": false,
        "id": 616
      }
    ]
  },
  (...)
]
```

### Parameters

| Name   | Type   | Description                                                                                                                         |
|--------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| embed  | string | Embed allows you to retrieve in a single call an order as well as *products* and the *customer* objects.                            |
| filters| string | Filters applied orders.                                                                                                             |
| order  | string | Sorting applied to orders.                                                                  										|

As this method can return multiple results, the usual [pagination parameters](#pagination) apply.

#### Embeds

The following objects can be embedded.

- **products** : will embed the related *product* object for each [*item*](#item-object) in the order.
- **customer** : will include the related [*customer*](#customers) object.

Multiple embeds can be requested by separating them with a comma (e.g. `?embed=products,customer`).

#### Filters

The following filters can be applied:

- **customer** : will only return orders for the specified customer id.
- **status** : only fetch orders with the request status.

Filters are cumulatives and should be separated by a comma. For example, `?filters=customer:1234,status:pending` will only return orders from customer 1234 with a *pending* status.

#### Order

Orders can be sorted by

- **id** : by ascending *id*
- **-id** : by descending *id*
- **date** : ascending *date*
- **-date** : descending *date*
- **-price** : *date*, descending
- **status** : grouped by status, alphabetically
- **-status** : grouped by status, reverse alphabetically
- **total** : total price, ascending
- **-total** : total price, descending

Only one sorting parameter is allowed (e.g. `&order=-date`).


## Create order

`POST /api/1/orders`

> Example request

```shell
curl -X POST \
	-H 'X-Kem-User: ' \
	-H 'X-Kem-Signature: ' \
	-H 'Content-Type: application/json' \
	-H 'Accept-Language: en' \
	-d '{ \
		"return_url":"https://example.com/success", \
		"customer": {"email":"shirley@example.com"}, \
		"shipping": {"method":"DOM.EP", "signature":"1432685850:l0esPFkm0VyT:434d400b84f7e350423fded032c32029b4a3bbca76a859de25f915ff42db5a5c","price":"7.86"}, \
		"items": [{"id":123,"quantity":1}], \
		"shipping_address": {"postcode":"H2V 4G7","country":"CA","province":"QC","line1":"5412 Park", "name":"Shirley Bennett","city":"Montreal","phone":"+15143035667"}}' \
	https://api.kem.guru/api/1/orders
```

```php
<?php

$body = <<<EOT

{
    "return_url": "https://example.com/success",
    "customer": {
        "email": "shirley@example.com"
    },
    "shipping": {
        "method": "DOM.EP",
        "signature": "1432685850:l0esPFkm0VyT:434d400b84f7e350423fded032c32029b4a3bbca76a859de25f915ff42db5a5c",
        "price": "7.86"
    },
    "items": [
		{
        "id": 123,
        "quantity": 1
    	}
	],
    "shipping_address": {
        "postcode": "H2V 4G7",
        "country": "CA",
        "province": "QC",
        "line1": "5412 Park",
        "name": "Shirley Bennett",
        "city": "Montreal",
        "phone": "+15143035667"
    }
}

EOT;

$response = Guzzle::post('https://api.kem.guru/api/1/orders/estimate', [
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
endpoint = "https://api.kem.guru/api/1/orders/estimate"
headers = {
	"X-Kem-User": "",
	"X-Kem-Signature":"",
	"Content-Type": "application/json",
	"Accept-Language":"en"
}
payload = """
{
    "return_url": "https://example.com/success",
    "customer": {
        "email": "shirley@example.com"
    },
    "shipping": {
        "method": "DOM.EP",
        "signature": "1432685850:l0esPFkm0VyT:434d400b84f7e350423fded032c32029b4a3bbca76a859de25f915ff42db5a5c",
        "price": "7.86"
    },
    "items": [
		{
        "id": 123,
        "quantity": 1
    	}
	],
    "shipping_address": {
        "postcode": "H2V 4G7",
        "country": "CA",
        "province": "QC",
        "line1": "5412 Park",
        "name": "Shirley Bennett",
        "city": "Montreal",
        "phone": "+15143035667"
    }
}
"""
r = requests.post(endpoint, headers=headers, data=payload)
```

> Example response

```json

{
    "id": 1007,
    "payment_details": {
        "total": 60.76,
        "subtotal": 44.9,
        "taxes": 7.91,
        "shipping": 7.95,
        "currency": "CAD",
        "payment_url": "https:\\/\\/pay.kem.guru\\/order\\/1234\\/4321?success=https%3A%2F%2Fexample.com%2Fsuccess",
        "balance": 60.76
    },
    "status": "pending",
    "shipping_address": {
        "line1": "5412 Park",
        "line2": null,
        "name": "Shirley Bennett",
        "postcode": "H2V 4G7",
        "city": "Montreal",
        "province": "QC",
        "country": "CA",
        "phone": "+15143035667"
    },
    "billing_address": {
        "line1": "5412 Park",
        "line2": null,
        "name": "Shirley Bennett",
        "postcode": "H2V 4G7",
        "city": "Montreal",
        "province": "QC",
        "country": "CA",
        "phone": "+15143035667"
    },
    "production": false,
    "verification": 5680,
    "date": null,
    "customer": {
        "id": 1476,
        "email": "shirley@example.com",
        "phone": "+15143035667",
        "postcode": "H2V 4G7",
        "name": "Shirley Bennett",
        "locale": {
            "id": "en-CA",
            "name": "English Canada",
            "language": "en",
            "language_name": "English",
            "script": "Latn"
        },
        "metadata": null,
        "production": true
    },
    "items": [{
        "quantity": 1,
        "price": 44.9,
        "canceled": false,
        "shipped": false,
        "id": 123,
        "product": {
            "id": 123,
            "price": 19.95,
            "taxable": true,
            "barcode": "123456789012",
            "sku": "B003SXGN7K",
            "weight": 0.23,
            "enabled": true,
            "discontinued": false,
            "reduced_price": {
                "price": 15.95
            },
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
            "images": [{
                "id": 9876,
                "url": "https:\\/\\/img.kem.guru\\/product-8-8915-troy-abed-morning-mug-{width}-{height}-{mode}"
            }],
            "inventory": {
                "count": 0
            }
        }
    }]
}

```

### Parameters

| Name | Type | Description |
|------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| return_url | string | An optional return url, where the payment gateway will redirect the customer after the payment is processed. Your return url should use the http:// or https:// protocols. If you need the API to support another protocol (to jump back to your app, for example), just let us know. An `&order=` parameter will be added to the return url with the order number. |
| customer | object | A complete or partial [customer](#customers) object. If an `id` is passed, the order will automatically be associated with the existing customer and other present fields will update the customer object. If no `id` is included, the API will attempt to create a new customer with the provided field data. If a customer already exists with the same phone number or email address, the order will automatically be attached to that existing customer, and any other included field data will be used to update that customer. |
| items | array | A list of [items](#item-object). Only the `id` and `quantity` are required, any other parameter will be ignored. |
| shipping | object | A complete or partial [shipping](#shipping-object) object. Only the `method` field is required. Passing back the entire object retrieved in the [estimate](#estimate-taxes-and-shipping) call is considered a best practice. |
| shipping_address | object | An [address](#addresses) object, where the order will be ultimately shipped. |
| billing_address | object | An optional [address](#addresses). If the parameter is omitted or is `null`, the `shipping_address` will be used instead. |

### Returned parameters

The returned order always include the full [order](#orders) object as well as all applicable embeds (*products* and *customers*). 