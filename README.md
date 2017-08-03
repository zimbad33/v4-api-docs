Overview
========

The purpose of this document is to provide detailed descriptions for RewardOps API calls, parameters and responses. These can be tested using our [interactive API console](https://pangea-int.rewardops.net/api_docs/console).

Introduction
============

## Security

### HTTPS

All requests must be performed using SSL via the HTTPS protocol.

### OAuth

RewardOps uses [OAuth 2.0](http://oauth.net/2/) with the Client Credentials grant type. This grant type requires you to use your `client_id` and `client_secret` to create a temporary access token, which is sent with subsequent API requests.

#### Generating an access token

To get an access token, send a POST request to `https://app.rewardops.net/api/v4/auth/token`. The request must have an `Authorization` field in the header. The value of the field must be `Basic $ENCODED_CREDENTIALS`, where `$ENCODED_CREDENTIALS` is your `client_id` and `client_secret`, separated by a colon (`client_id:client_secret`) and base64 encoded.

The JSON response will contain an `access_token` that you can use in subsequent requests. Tokens are valid for two hours. When your token has expired, you'll need to request another token before making further API calls. The API server will respond with an error when you make a request using an expired or otherwise invalid token.

##### Example

```nginx
# Base64-encode the client_id and client_secret, separated by a colon. Then use the base64 string in the Authorization header.

echo -n "CLIENT_ID:CLIENT_SECRET" | openssl enc -base64 -A | { read ENCODED_CREDENTIALS; curl https://demo.rewardops.net/api/v4/auth/token --header "Authorization: Basic $ENCODED_CREDENTIALS" --data "grant_type=client_credentials" --verbose; }

# Response:

{"access_token":"ac7523d4011492d83bfa1ecd5f62ec3c3a1962e1fee84fa53305fc7c75d29c8b","token_type":"bearer","expires_in":7200,"created_at":1428705205}
```

#### Using access tokens

Once you've generated an access token, you can use it to make requests to the API. All API requests must have an `Authorization` field in the header. The value of the field must be `Bearer $ACCESS_TOKEN`, where `$ACCESS_TOKEN` is the valid access token that you created.

## Versioning

Different versions of the API have different base URLs. The base URL for API v4 is `https://app.rewardops.net/api/v4`.

## Request headers

| Headers |   |
|---------|---|
| **Authorization** | Must contain `Bearer $ACCESS_TOKEN`, where `$ACCESS_TOKEN` is a valid access token |
| **Accept** | Specify the desired response format [optional [application/json]] |
| **Content-Type** | Specify the format of the data payload [optional [application/json]] |

Parameters
==========

The default format for data passed to and from the API is JSON, with the exception of header and URL parameters. The format of the request and response data can be specified by using the Content-Type and Accept headers, respectively. Currently, only JSON is supported and therefore these headers can be ignored at this time.

Validation is performed on the input parameters for each endpoint, according to the type of the parameter. If validation fails for any of the provided parameters, a 400 error is returned with a detailed error message in the response.

Additionally, each string passed to or from the API must be UTF-8 encoded and dates passed to or from the API must follow the ISO 8601 combined date/time format.

## Data types

| Types |   |
|-------|---|
| **integer** | Only numerals [0-9], and an optional leading "-" character to indicate a negative value. |
| **decimal** | Only numerals [0-9], an optional decimal ("."), and an optional leading "-" character to indicate a negative value. |
| **string** | Any characters, but "unsafe" character combinations are stripped (HTML tags, SQL statements, etc.). Must be UTF-8 encoded. |
| **date** | A string representing a date is expected, using ISO 8601 combined date and time format (eg. 2013-08-08T18:34Z). |
| **boolean** | A JSON boolean value (`true` or `false`). |

Status Codes
============

The API employs standard HTTP status codes to indicate the general success or failure of the call to the API server. A complete list of the possible status codes is itemized below.

*2xx* - General success  
*4xx* - Error occurred due to the client's request  
*5xx* - Error occurred within the server

| Status codes |   |
|--------------|---|
| **200** | OK |
| **400** | Bad request (Bad data in parameters, etc) |
| **401** | Unauthorized (Attempt to access a resource outside your domain) |
| **403** | Forbidden (Problem with your credentials or access token) |
| **404** | Not found (Resource or service not found) |
| **409** | Conflict (Problem with the resource and/or service request) |
| **500** | Internal server error |
| **502** | Remote API error |

##### Example Response

```json
"status": "error",
  "result": {
    "error": {
	  "code": 400,
	  "reason": "Parameter Error",
	  "detail": "Validation failed for parameter program_id: is not of type integer"
    }
  }
}
```

Filters
============

The filter query string parameter restricts the number of entries returned by an API call. Filters can currently be applied against the `GET Items` and `GET Items Bulk` calls.

A filter is comprised of one or many comparisons, chained together with boolean operations.

## Filter Descriptions

| Filter | Description  |
|-------|---|
| **ID** | Match against a specific supplier_item id |
| **REGULAR_PRICE** | Match against a supplier_item price, in subunits of the price currency (ex. for dollars, $10 = 1000 (one thousand cents) |
| **SALE_PRICE** | Match against a supplier_item price, in subunits of the price currency (ex. for dollars, $10 = 1000 (one thousand cents) |
| **LABEL** | Match against item labels of the following type: `Brand`, `Artist` |
| **CLABEL** | Match against item labels of the following type: `Filter`, `Pricing` |
| **CATEGORY** | Match against an item category, specified in parenthesis (an ID or category code). The comparison value must be true or false, indicating whether to match against only the specified category or the specified category and all of its children categories |
| **CCATEGORY** | Match against an item custom category, specified in parenthesis (an ID or custom category code).  The comparison value must be true or false, indicating whether to match against only the specified category or the specified category and all of it's children categories |
| **PROPERTY** | Match against a property, specified in parenthesis (an ID or property in within quotation marks). |
| **CPROPERTY** | Match against a custom property, specified in parenthesis (an ID or property in within quotation marks). |

## Filter Parameters

| Filter | Identifier  |
|-------|---|
| **ID** | `Integer` |
| **REGULAR_PRICE** | `Integer` |
| **SALE_PRICE** | `Integer` |
| **LABEL** | `String`, `Integer`; Supported Labels: Brand, Artist |
| **CATEGORY** | `Integer`(ID), `String` (Code); `Boolean` (to include full subtree) |
| **CCATEGORY** | `Integer`(ID), `String` (Code); `Boolean` (to include full subtree) |
| **PROPERTY** | `Integer (ID)`, `String (Code)`; |
| **CPROPERTY** | `Integer (ID)`, `String (Code)`; |

## Boolean Operators

| Types |   |
|-------|---|
| **&** | Logical AND |
| **|** | Logical OR |

## Comparison Operators

| Types |   |
|-------|---|
| **==** | Equals |
| **!=** | Not Equal |
| **>** | Equals |
| **<** | Not Equal |
| **>=** | Greater than or equal to |
| **<=** | Less than or equal to |

## Examples

### Filter items by category CAT_SYS_000005

`/api/v4/programs/6254/items?filter=CATEGORY("CAT_SYS_000005")==true`

### Filter items by categories CAT_SYS_000005 OR CAT_SYS_000006

`/api/v4/programs/6254/items?filter=CATEGORY("CAT_SYS_000005")==true|CATEGORY("CAT_SYS_000006")==true`

### Filter items by property variant color equal to Black

`/api/v4/programs/6254/items?filter=PROPERTY("color")=="Black"`

### Filter items by brand Samsung and category CAT_SYS_000010 (Phones)

`/api/v4/programs/6254/items?filter=LABEL(Brand)=="Samsung"&CATEGORY("CAT_SYS_000010")=="true"`

### Filter items by regular price greater than or equal to $10.99

`/api/v4/programs/6254/items?filter=REGULAR_PRICE>=1099`

API Endpoints
=============

## Items

This set of endpoints facilitates the navigation of the rewards catalog by an end user.

* List all items (bulk call)
* List all items (in an optional segment)
* Retrieve an item (by item id for a program, in an optional segment)

## Orders

This set of endpoints facilitates the placement of an order by a rewards program on behalf of an end user.

* Create an order
* List all orders
* Retrieve an order
* Update an order

## Supplier Orders

* Create a supplier order
* Create a supplier order asset
* List all supplier orders
* Retrieve a supplier order
* Update a supplier order

Items
=====

Items which will appear in your rewards catalog are stored in item objects, and item objects are composed of item variant objects along with several sub objects: Categories, Brands, Properties, Variant Properties, and Images.

## The item object

| Attributes |   |
|------------|---|
| **id** integer | Unique identifier assigned to each item |
| **name** string | Item name |
| **model** string | Manufacturer item model number |
| **industry_id** string | Universal product code (UPC) |
| **images** object | An [images object](#the-images-sub-object) for the item |
| **type** string | Item type (Merchandise, Gift Card, Periodical, Donation, Event, Travel, Music, Video, Book, Discount, Offsite) |
| **description** string | Item description |
| **specifications** string | Item specifications |
| **notes** string | Item notes |
| **categories** object | A [categories object](#the-categories-sub-object) for the item |
| **brands** object, optional | A [brands object](#the-brands-sub-object) for the item |
| **properties** object, optional | A [properties object](#the-properties-sub-object) for the item |
| **variant_properties** object, optional | A [variant properties object](#the-variant-properties-sub-object) for the item |
| **supplier_items** object | A [supplier item variant object](#the-supplier-item-variant-sub-object) for the item |

##### Example item object

```json
{
  "status": "OK",
  "result": {
    "items": [
      {
        "id": 1,
        "name": "AAXA - M4 Pico DLP Portable Projector - White",
        "model": "MP-400-01",
        "industry_id": "861495000155",
        "description": "Watch the next big game or give a presentation with ease using this AAXA M4 MP-400-01 projector, which is cable-ready, so you can catch your favorite programs (antenna not included) on a screen size that adjusts from 15\" to 150\" to suit your needs.Not sure what size projector or screen you need? Learn more.",
        "specifications": "{\"Lamp Type\":\"LED\",\"Smart Capable\":\"No\",\"Stereo Jack\":\"Yes\",\"Color Category\":\"White\",\"Display Type\":\"DLP\",\"Remote Control Included\":\"Yes\",\"Speaker(s) Included\":\"Yes\",\"White Brightness\":\"800 lumens\",\"Minimum Viewable Screen Size\":\"15 inches\",\"Projector Type\":\"Pico\",\"Throw Ratio Range\":\"1.36:1\",\"Wireless\":\"No\",\"Instant Content Supported\":\"None\",\"Input(s)\":\"USB 2.0|HDMI|Composite video|VGA\",\"Number Of USB Port(s)\":\"1\",\"Contrast Ratio\":\"2,000:1\",\"Minimum Projector Distance\":\"1.43 feet\",\"TV Tuner\":\"Yes\",\"Digital Keystone Correction\":\"Yes\",\"Lamp Included\":\"Yes\",\"Vertical Resolution\":\"1080p\",\"Computer Compatibility\":\"Android|Apple iOS|Mac|Windows\",\"Lamp Life\":\"20000 hours\",\"Number Of Audio Outputs\":\"1\",\"ENERGY STAR Certified\":\"No\",\"Number Of HDMI Inputs\":\"1\",\"TV/Cable Ready\":\"Yes\",\"Maximum Resolution\":\"1920 x 1080\",\"Aspect Ratio\":\"16:9\",\"Resolution (Native)\":\"1280 x 800\",\"Portable\":\"Yes\",\"Audio Output(s)\":\"3.5mm audio\",\"Maximum Projector Distance\":\"14.3 feet\",\"3D Technology\":\"No\",\"Maximum Viewable Screen Size\":\"150 inches\",\"Video Input(s)\":\"HDMI|Composite video|VGA\",\"pricematch\":\"yes\",\"depth\":\"8.3\\\"\",\"height\":\"3.1\\\"\",\"weight\":\"2.44 pounds\",\"width\":\"5.3\\\"\",\"shippingWeight\":\"4.37\",\"warrantyLabor\":\"1 year limited\",\"warrantyParts\":\"1 year limited\",\"type\":\"HardGood\"}",
        "images": [
          {
            "name": "primary",
            "description": "primary photo",
            "sizes": {
              "350x350": "http://s3.amazonaws.com/pangea-app-development/item_variants/images/000/000/001/350x350/4338628_sa.jpg?1454024427",
              "74x74": "http://s3.amazonaws.com/pangea-app-development/item_variants/images/000/000/001/74x74/4338628_sa.jpg?1454024427",
              "46x46": "http://s3.amazonaws.com/pangea-app-development/item_variants/images/000/000/001/46x46/4338628_sa.jpg?1454024427"
            }
          }
        ],
        "notes": "Weighs only 2.44 lbs. and measures 3.1\" thin\nFits easily in your briefcase or bag to simplify portability. The lithium-ion battery promotes portable use.\nConnect your compatible mobile device\nEnjoy mobile content in 1280 x 800 resolution with a 16:9 aspect ratio. View family photos and other content using the microSD card slot (memory card not included).\nDLP technology\nWith Pico technology produces reliable picture.\n800 lumens white brightness\nFor a bright, highly visible display.\n2,000:1 contrast ratio\nProvides a wide range of shades between black and white.\nLED light source\nOffers up to 20,000 hours of illumination.\nDigital keystone correction\nHelps minimize distortion.\nSupports 1280 x 720 native resolution\nVia the HDMI port. Use the DTV tuner to access cable, so you can catch your favorite primetime programs (over-the-air antenna required, not included).\nDual 2W speakers\nProduce high-quality audio.\nApple, the Apple logo and iTunes are trademarks of Apple Computer, Inc., registered in the U.S. and other countries. iPod is a trademark of Apple Computer, Inc.",
        "type": "merchandise",
        "categories": [
          {
            "path": "Root/TV & Video",
            "code": "CAT_SYS_000002",
            "labels": [
              "TV & Video"
            ]
          }
        ],
        "brands": [
          {
            "name": "AAXA",
            "url": null,
            "image_url": null
          }
        ],
        "properties": [
          {
            "name": "Height (in)",
            "key": "height_in",
            "value": "3.1\""
          },
          {
            "name": "Width (in)",
            "key": "width_in",
            "value": "5.3\""
          },
          {
            "name": "Depth (in)",
            "key": "depth_in",
            "value": "8.3\""
          },
          {
            "name": "Weight (lb)",
            "key": "weight_lb",
            "value": "2.44"
          },
          {
            "name": "Shipping Weight (lb)",
            "key": "shipping_weight_lb",
            "value": "4.37"
          },
          {
            "name": "Warranty (Labour)",
            "key": "warranty_labour",
            "value": "1 year limited"
          },
          {
            "name": "Warranty (Parts)",
            "key": "warranty_parts",
            "value": "1 year limited"
          }
        ],
        "variant_properties": [
          {
            "name": "Colour",
            "key": "colour",
            "value": "white"
          },
          {
            "name": "Format",
            "key": "format",
            "value": "hardgood"
          }
        ],
        "supplier_items": [
          {
            "id": 1,
            "external_supplier_id": "4338628",
            "product_url": "http://www.bestbuy.com/site/aaxa-m4-pico-dlp-portable-projector-white/4338628.p?id=1219733828305&skuId=4338628",
            "reviews_rating": null,
            "fulfillment": {
              "requirements": [
                "id",
                "full_name",
                "email",
                "address"
              ],
              "instructions": "Delivery to most areas within 3-5 business days",
              "terms_and_conditions": "",
              "shipping_estimate": {
                "USD": "0.0"
              },
              "shipping_included": true,
              "handling_estimate": {
                "USD": "0.0"
              },
              "handling_included": true,
              "geo_targetting": {
                "include": [
                  "US"
                ],
                "exclude": [
                  "US-AK",
                  "US-PR"
                ]
              }
            },
            "pricing": {
              "USD": {
                "regular_price": "514.42",
                "sale_price": "599.99"
              },
              "tax_eligible": true
            },
            "order_token": "aKhDNFoysSM0_0VTpD95hQ==",
            "offer": {
              "id": 1,
              "starts_at": "2015-07-31T21:20:11.000Z",
              "ends_at": "2015-07-31T21:20:11.000Z",
              "updated_at": "2015-07-31T21:20:11.000Z",
              "name": "Best Buy Offer",
              "type": "simple",
              "supplier_id": 1,
              "supplier_name": "BestBuy"
            }
          }
        ]
      }
    ]
  },
  "parameters": {
    "start_date": "2015-01-01T00:00:00.000+00:00",
    "end_date": "2016-02-19T21:17:15.401+00:00",
    "locale_code": "en-US"
  },
  "pagination": {
    "previous": null,
    "next": 2,
    "current": 1,
    "per_page_count": 1,
    "count": 2362,
    "pages": 2362
  }
}
```

### The supplier item variant sub object

| Attributes |   |
|------------|---|
| **id** string | Unique RewardOps identifier for the supplier item variant |
| **external_supplier_id** string | Unique identifier assigned to each supplier item |
| **fulfillment** object | A [fulfillment object](#the-fulfillment-sub-object) for the supplier item variant |
| **pricing** object | A [pricing object](#the-pricing-sub-object) for the supplier item variant |
| **order_token** string | Token used when ordering the supplier item variant |
| **availability_status** string | Indicates the availability of the item in the catalog. Possible values are `available`, `out_of_stock`, or `inactive` |
| **offer** | An [offer object](#the-offer-sub-object) describes the details of the supplier's item offer to the program|
| **geo_targeting** object | A [geo targeting object](#the-geo-targeting-sub-object) for the supplier item variant |

### The geo targeting sub object

| Attributes |   |
|------------|---|
| **include** array | A collection of regions and sub-regions to include |
| **exclude** array | A collection of regions and sub-regions to exclude |

### The fulfillment sub object

| Attributes |   |
|------------|---|
| **requirement** array | A collection of member details that must be submitted to fulfill the item |
| **instructions** string | Supplier-specific fulfillment instructions that should be displayed to the member |
| **terms_and_conditions** string | Supplier-specific terms and conditions that should be displayed to the member |
| **shipping_included** boolean | Indicates whether shipping is included in the final member price |
| **shipping_estimate** decimal | Estimated shipping cost |
| **handling_included** boolean | Indicates whether shipping is included in the final member price |
| **handling_estimate** decimal | Estimated handling cost |

### The pricing sub object

| Attributes |   |
|------------|---|
| **regular_price** decimal | An item's regular price |
| **sale_price** decimal | An item's sale price |
| **tax_eligible** boolean | Incremental cost for sales tax |

### The offer sub object

| Attributes |   |
|------------|---|
| **id** integer | Unique identifier assigned to each offer |
| **name** string | Offer name |
| **supplier_id** integer | Unique identifier of the supplier |
| **type** string | Offer type |
| **starts_at** string | Offer start date |
| **ends_at** string | Offer end date |
| **updated_at** string | Offer last updated date |

### The categories sub object

| Attributes |   |
|------------|---|
| **path** string | The full path of the item's categories and sub-categories |
| **code** string | Unique identifier assigned to each category |
| **labels** string | Labels associated with the items category |

### The brands sub object

| Attributes |   |
|------------|---|
| **name** string | Name of the brand |
| **url** string | Url of the brand |
| **image_url** string | Url of the brand logo |

### The properties sub object

| Attributes |   |
|------------|---|
| **name** string | Property name |
| **key** string | Property key |
| **value** string | Property value |

### The variant properties sub object

| Attributes |   |
|------------|---|
| **name** string | Variant property name |
| **key** string | Variant property key |
| **value** string | Variant property value |
| **other_variants** hash, optional | A collection of item ids and names for variant properties |

### The images sub object

| Attributes |   |
|------------|---|
| **name** string | Name of image |
| **description** string | Short description of image |
| **350x350** string | Url of the image with a resolution of 350x350 |
| **74x74** string | Url of the image with a resolution of 74x74 |
| **46x46** string | Url of the image with a resolution of 46x46 |

## List all items (bulk call)

Returns a list of program items.

| Arguments |   |
|-----------|---|
| **program_id** integer, required | Unique identifier assigned to each program item |
| **segment_id** integer, optional | Unique identifier assigned to each program segment |
| **start_date** string (ISO8601 Combined date and time in UTC), required | Start Date for date range of changed items | 
| **end_date** string (ISO8601 Combined date and time in UTC), optional | End Date for date range of changed items | 
| **accept_language** string, optional | in the format of Accept-Language as per RFC2616 (defaults to program settings) ex. 'en-CA, en-US' |
| **filter** integer, optional | Full filter specification |
| **page** integer, optional | The page number returned |
| **page_per_count** integer, optional | Records returned per page |
| **filter_category_code** string, optional | (DEPRECATED: See FILTER) Filter (Program) category code to filter results by |
| **filter_category_subtree** string, optional | DEPRECATED: See FILTER) Boolean indicating if the full subtree of the provided filter category code should be included |
| **category_code** string, optional | (DEPRECATED: See FILTER) (System) Category code to filter results by |
| **category_subtree** boolean, optional | (DEPRECATED: See FILTER) Boolean indicating if the full subtree of the provided system category code should be included |

##### Endpoint

```nginx
GET /api/v4/programs/{program_id}/items/bulk
```

## List all items

Returns a list of program items.

| Arguments |   |
|-----------|---|
| **program_id** integer, required | Unique identifier assigned to each program item |
| **segment_id** integer, optional | Unique identifier assigned to each program segment |
| **accept_language** string, optional | in the format of Accept-Language as per RFC2616 (defaults to program settings) ex. 'en-CA, en-US' |
| **detailed** integer, optional | Return detailed objects (defaults to false) |
| **sort_by** string, optional | Sort results by [["name", "sale_price", "created_date"]] |
| **sort_order** string, optional | Order the results [ascending, descending] |
| **page** integer, optional | The page number returned |
| **page_per_count** integer, optional | Records returned per page |
| **q** integer, optional | Search term in item name, model, industry id and description |
| **filter_category_code** string, optional | (DEPRECATED: See FILTER) Filter (Program) category code to filter results by |
| **filter_category_subtree** string, optional | DEPRECATED: See FILTER) Boolean indicating if the full subtree of the provided filter category code should be included |
| **category_code** string, optional | (DEPRECATED: See FILTER) (System) Category code to filter results by |
| **category_subtree** boolean, optional | (DEPRECATED: See FILTER) Boolean indicating if the full subtree of the provided system category code should be included |

##### Endpoint

```nginx
GET /api/v4/programs/{program_id}/items
```

## Retrieve an item

Retrieves the deatils of an item.

| Arguments |   |
|-----------|---|
| **id** integer, required | Unique identifier assigned to each program item |
| **program_id** integer, required | Unique identifier assigned to each program |
| **segment_id** integer, optional | Unique identifier assigned to each program segment |
| **accept_language** string, optional | in the format of Accept-Language as per RFC2616 (defaults to program settings) ex. 'en-CA, en-US' |

##### Endpoint

```nginx
GET /api/v4/programs/{program_id}/items/{id}
```

Orders
======

The placement of an order (by a rewards programs or a supplier, on behalf of an end user) is handled through the creation of order objects. Orders are composed of several sub objects: Member, Order Supplier, and Order Item.

## The order object

| Attributes |   |
|------------|---|
| **id** string | Unique RewardOps generated identifier |
| **program_order_id** string | A reward program's internal order id, if supplied. |
| **program_id** integer | Unique identifier assigned to the program where the order originated from |
| **created_at** string | Order creation date |
| **updated_at** string | Order last updated date |
| **status** string | Possible values are `pending`, `partial`, `stopped`, or `inactive`. |
| **payment_status** string | Possible values are `pending`, `paid`, `rejected`, or `refunded`. |
| **member** object | A [member object](#the-member-sub-object) for the order, containing details of the reward program member that placed the order. |
| **supplier** object | A [supplier object](#the-supplier-sub-object) for the order. |

##### Example order object

```json
{
  "status": "OK",
  "result": {
    "order": {
      "id": "56bbc2a4cf8e76",
      "program_order_id": "10348439383",
      "program_id": 1,
      "created_at": "2016-02-19T21:34:21.668Z",
      "updated_at": "2016-02-19T21:34:21.668Z",
      "status": "PENDING",
      "payment_status": "PAID",
      "member": {
        "id": "272827",
        "full_name": "John Smith",
        "email": "johnsmith@gmail.com",
        "phone": "9174345456",
        "address": {
          "address": "123 Main Street",
          "address_2": "Apt 203",
          "city": "New York",
          "country_code": "US",
          "country_subregion_code": "NY",
          "postal_code": "10012"
        }
      },
      "suppliers": [
        {
          "name": "BestBuy",
          "id": 22,
          "member_paid":  {
            "XRO-PTS": "100.00"
          },
          "program_cost": {
            "USD": {
              "currency_id": "1",
              "shipping": "10.82982",
              "handling": "0.0",
              "handling_tax": "0.0",
              "estimated_import_fees": "0.0",
              "adjustments_total": "0.0"
            }
          },
          "order_events": [],
          "items": [
            {
              "id": "1c38c7e104b780",
              "supplier_item_id": 1,
              "name": "AAXA - M4 Pico DLP Portable Projector - White",
              "type": "merchandise",
              "description": "Watch the next big game or give a presentation with ease using this AAXA M4 MP-400-01 projector, which is cable-ready, so you can catch your favorite programs (antenna not included) on a screen size that adjusts from 15\" to 150\" to suit your needs.Not sure what size projector or screen you need? Learn more.",
              "images": [
                {
                  "name": "primary",
                  "description": "primary_photo",
                  "sizes": {
                    "350x350": "http://s3.amazonaws.com/pangea-app-development/item_variants/images/000/000/001/350x350/4338628_sa.jpg?1454024427",
                    "74x74": "http://s3.amazonaws.com/pangea-app-development/item_variants/images/000/000/001/74x74/4338628_sa.jpg?1454024427",
                    "46x46": "http://s3.amazonaws.com/pangea-app-development/item_variants/images/000/000/001/46x46/4338628_sa.jpg?1454024427"
                  }
                }
              ],
              "member_paid": {
                "XRO-PTS": "5000.00"
              },
              "program_cost": {
                "USD": {
                  "item": "541.49",
                  "tax": "48.06"
                }
              },
              "retail_value": {},
              "fulfillment": {
                "terms_and_conditions": "",
                "instructions": "Delivery to most areas within 3-5 business days",
                "status": "PENDING",
                "status_notes": null,
                "supplier_order_id": "BBY01-2257931869483",
                "carrier_name": "USPS",
                "carrier_tracking": null,
                "carrier_url": null,
                "carrier_delivery_date": null
              },
              "assets": {}
            }
          ]
        }
      ]
    }
  }
}
```

### The member sub object

| Attributes |   |
|------------|---|
| **id** string | Unique identifier assigned to each member of the program |
| **full_name** string | First and last name of the member |
| **email** string | Email address of the member |
| **phone** string | Mobile phone number of the member |
| **address** object | An [address object](#the-address-sub-object) for the member, containing the mailing address of the member |

### The address sub object

| Attributes |   |
|------------|---|
| **address** string | Address line 1 |
| **address_2** string | Address line 2 |
| **city** string | City |
| **country_code** string | Country code (ISO 3116) |
| **country_subregion_code** string | Sub-region code (ISO 3116)  |
| **postal_code** string | Postal/Zip code |

### The supplier sub object

| Attributes |   |
|------------|---|
| **id** integer | Unique identifier assigned to each supplier |
| **name** string | Supplier name |
| **member_paid** hash | Currency and amount reward program member paid for the item |
| **program_cost** object | A program cost object for the supplier |
| **order_events** object | An order event object for the supplier |
| **items** object | An item object for the supplier |

### The program cost sub object

| Attributes |   |
|------------|---|
| **currency_id** string | Currency identifier  |
| **shipping** decimal | Amount of shipping costs incurred by the reward program |
| **handling** decimal | Amount of handling costs incurred by the reward program |
| **handling_tax** decimal | Amount of handling tax costs incurred by the reward program |
| **estimated_import_fees** decimal | Amount of estimated import fee costs incurred by the reward program |
| **adjustments_total** decimal | Total amount of adjustments to the cost of an order |

### The order event sub object

| Attributes |   |
|------------|---|
| **description** string | A description of the order event. |
| **type** string | Possible values are `notice`, `refund`, or `fee`. |
| **date** string | Order event date. |
| **amount** decimal | Currency and amount of adjustment to the cost of the order. |

### The order supplier items sub object

| Attributes |   |
|------------|---|
| **id** integer | Unique RewardOps generated identifier |
| **supplier_item_id** integer | Unique identifier for the supplier item variant |
| **name** string | Item name |
| **type** string | Possible values are `merchandise`, `gift_card`, `periodical`, `donation`, `event`, `travel`, `music`, `video`, `book`, `discount`, or `discount`. |
| **description** string | Item description |
| **images** object | An [images object](#the-images-sub-object) for the item |
| **member_paid** hash | Currency and amount reward program member paid for the item |
| **program_cost** object | The program cost object for the order supplier item, containing the currency and amount program paid for the item (including sales tax). |
| **retail_value** decimal | Retail value of item |
| **fulfillment** object | The fulfillment object for the order supplier item. |
| **assets** object | An [assets object](#the-assets-sub-object) for the item |

### The fulfillment sub object

| Attributes |   |
|------------|---|
| **terms_and_conditions** string | Supplier-specific terms and conditions. |
| **instructions** string | Supplier-specific details pertaining to the delivery of the delivery of the reward. |
| **status** string | Possible values are `pending`, `processing`, `sent`, `delivered`, `rejected`, `returned`, or `canceled`. |
| **status_notes** string | notes that further describe details pertaining to the status |
| **supplier_order_id** string | order id provided by the supplier |
| **carrier_name** string | Name of the shipping carrier. |
| **carrier_tracking** string | Tracking number provided by the shipping carrier. |
| **carrier_url** string | Shipping carrier's tracking url for the item. |
| **carrier_delivery_date** string | Shipping carrier's estimated delivery date. |

### The asset sub object

| Attributes |   |
|------------|---|
| **codes** array of codes | An array of code assets that are required to redeem a reward. |
| **links** array of links | An array of link assets that are required to redeem a reward. |
| **images** array of images | An array of image assets that are required to redeem a reward. |

### The code sub object

| Attributes |   |
|------------|---|
| **label** string | Name of the asset being delivered. Defined by the brand providing the reward. (eg. Promo Code, Pin Code, Barcode) |
| **type** string | Type of reward asset. PLAIN is currently the only type. |
| **value** string | The code asset, delivered as an alphanumeric code. |

### The link sub object

| Attributes |   |
|------------|---|
| **label** string | Name of the asset being delivered. Defined by the brand providing the reward. (eg. Website, Magazine, MP3, eBook) |
| **type** string | Type of reward asset. Options for external asset types include DOWNLOAD and REDIRECT. |
| **value** string | The link asset, delivered as a URL. |

### The image sub object

| Attributes |   |
|------------|---|
| **label** string | Name of the asset being delivered. Defined by the brand providing the reward. (eg. Barcode, Certificate, Coupon) |
| **type** string | Type of reward asset. PLAIN is currently the only type. |
| **value** string | The image asset, delivered as a URL. |

## Create an order

Creates a new order object, specific to rewards programs.

| Arguments |   |
|-----------|---|
| **program_id** integer, required | Unique identifier assigned to each rewards program. |
| **program_order_id** string, optional | A unique identifier supplied by the rewards program. |
| **payment_status** string, optional | Possible values are `PAID`, `PENDING`, `REJECTED`, or `REFUNDED`. By default, `payment_status` is set to `PAID`. |
| **payment_status_notes** string, optional | Notes pertaining to the payment status. |
| **detailed** boolean, optional | When set to true, full order details are returned/provided. |
| **items** object, required | A collection of [items](#the-items-sub-object) included in this order. |
| **member** object, required | A [member](#the-member-sub-object) for the order, containing details of the reward program member that placed the order. |
| **accept_language** string, optional | in the format of Accept-Language as per RFC2616 (defaults to program settings) ex. 'en-CA, en-US'. |

### The items sub object

| Attributes |   |
|------------|---|
| **item_order_token** string | order_token of the item being ordered (as supplied by rewards api) |
| **quantity** integer | The quantity of this item being ordered |
| **member_spend** object | The [member spend](#the-member-spend-sub-object) |
| **retail_value** object, optional | The [retail value](#the-retail-value-sub-object) (used for value of gift cards)

### The member spend sub object


| Attributes |   |
|------------|---|
| **currency_code** string | Either an ISO currency code (e.g. USD, CAD) or a custom code in the form "XRO-xxx" where "xxx" is the custom code for the points |
| **amount** string | The amount of the spend |


### The retail value sub object


| Attributes |   |
|------------|---|
| **currency_code** string | ISO currency code (e.g. USD, CAD) |
| **amount** string | The amount of the spend |

##### Endpoint

```nginx
POST /api/v4/programs/{program_id}/orders
```

## List all orders

Returns a list of orders for a specific member in your rewards program. The orders are returned sorted by reverse chronological order.

| Arguments |   |
|-----------|---|
| **program_id** integer, required | Unique identifier assigned to each program |
| **member_id** integer | Unique identifier assigned to each member |
| **start_date** string, optional | Start Date for date range of changed items (ISO8601 Combined date and time in UTC) |
| **end_date** string, optional | End Date for date range of changed items (ISO8601 Combined date and time in UTC) |
| **detailed** boolean, optional | Return detailed objects (default: false)  |
| **page** tag, optional | The page number to return. |
| **per_page_count** integer, optional | Number of orders returned per page. |
| **sort_by** string, optional | Sort reward results [newest, oldest]. |
| **accept_language** string, optional | in the format of Accept-Language as per RFC2616 (defaults to program settings) ex. 'en-CA, en-US'. |

##### Endpoint

```nginx
GET /api/v4/programs/{program_id}/orders
```

## Retrieve an order

Retrieves the details of an existing order, specific to rewards programs.

| Arguments |   |
|-----------|---|
| **program_id** integer, required | Unique identifier assigned to each program |
| **id** string, required | Unique identifier for the order |
| **use_program_order_id** boolean, optional | Dictates whether to use the program-supplied order id. By default, `use_program_order_id` is set to `false`. |
| **accept_language** string, optional | in the format of Accept-Language as per RFC2616 (defaults to program settings) ex. 'en-CA, en-US'. |

##### Endpoint

```nginx
GET /api/v4/programs/{program_id}/orders/{id}
```

## Update an order

Updates the payment status of an existing order, specific to rewards programs.

| Arguments |   |
|-----------|---|
| **program_id** integer, required | Unique identifier assigned to each program |
| **id** string, required | Unique identifier for the order |
| **payment_status** string, optional | Possible values are `PAID`, `PENDING`, `REJECTED`, or `REFUNDED`. By default, `payment_status` is set to `PAID`. |
| **payment_status_notes** string, optional | Notes pertaining to the payment status. |
| **use_program_order_id** boolean, optional | Use the program's ID rather than the RewardOps ID to identify this order (default false). |
| **accept_language** string, optional | in the format of Accept-Language as per RFC2616 (defaults to program settings) ex. 'en-CA, en-US'. |

```nginx
PATCH /api/v4/programs/{program_id}/orders/{id}
```

Supplier Orders
===============

## Create a supplier order

Creates a new supplier order object.

| Arguments |   |
|-----------|---|
| **supplier_id** integer, required | Unique identified assigned to each supplier.
| **program_id** integer, required | Unique identifier assigned to each rewards program. |
| **item_order_token** string, required | The token identifying the supplier's offsite catalog. |
| **supplier_order_id** string, optional | A unique identifier provided by the supplier. |
| **detailed** boolean, optional | When set to true, full order details are returned/provided. By default, `detailed` is set to `false`. | **member_paid** object, required | A member paid object for the supplier order. |
| **program_cost** object, required | A program cost object for the supplier order. |
| **items** object, required | A collection of supplier items included in this order. |
| **member** object, required | A [member object](#the-member-sub-object) for the order, containing details of the reward program member that placed the order. |

##### Endpoint

```nginx
POST /api/v4/suppliers/{supplier_id}/orders
```

## Create a supplier order asset

Creates a new supplier order object.

| Arguments |   |
|-----------|---|
| **supplier_id** integer, required | Unique identifier assigned to each supplier. |
| **order_id** string, required | Unique external identifier for the supplier order. |
| **order_item_id** string, required | Unique external identifier for an item in the supplier order. |
| **type** string, required | Find the allowed asset type with this Type: UNIQUE_CODE, UNIVERSAL_CODE, UNIQUE_REDIRECT_LINK, UNIVERSAL_REDIRECT_LINK, UNIQUE_IMAGE, UNIVERSAL_IMAGE, UNIQUE_DOWNLOAD_LINK, UNIVERSAL_DOWNLOAD_LINK, MAILED_ASSET, E_MAILED_ASSET, UNIQUE_EXTERNAL_CODE, UNIQUE_EXTERNAL_REDIRECT_LINK, UNIQUE_EXTERNAL_IMAGE, UNIQUE_EXTERNAL_DOWNLOAD_LINK |
| **label** string, required | The allowed asset type with this label. |
| **value** string, required | Value assigned to this asset. |

```nginx
POST /api/v4/suppliers/{supplier_id}/orders/{order_id}/order_items/{order_item_id}/order_assets
```

## List all supplier orders

Returns a list of orders for a specific supplier. The orders are returned sorted by reverse chronological order.

| Arguments |   |
|-----------|---|
| **supplier_id** integer, required | Unique identifier assigned to each supplier. |
| **start_date** string, optional | Start date of date range to query for orders. |
| **end_date** string, optional | End date of date range to query for orders. |
| **detailed** boolean, optional | Dictates whether full order details are returned/provided. By default, `detailed` is set to `false`. |
| **page** tag, optional | The page number to return. |
| **per_page_count** integer, optional | Number of orders returned per page. |
| **sort_by** string, optional | Sort reward results [newest, oldest]. |

##### Endpoint

```nginx
GET /api/v4/suppliers/{supplier_id}/orders
```

## Retrieve a supplier order

Retrieves the details of an existing order for a specific supplier.

| Arguments |   |
|-----------|---|
| **id** string, required | Unique external identifier for the order. |
| **supplier_id** integer, required | Unique identifier assigned to each supplier. |
| **use_supplier_order_id** boolean, optional | Dictates whether to use the supplier-supplied order id. By default, `use_supplier_order_id` is set to `false`. |

##### Endpoint

```nginx
GET /api/v4/programs/{supplier_id}/orders/{id}
```

## Update a supplier order

Updates the fulfillment status of an existing supplier order.

| Arguments |   |
|-----------|---|
| **supplier_id** integer, required | Unique identifier assigned to each supplier. |
| **order_id** string, required | Unique external identifier for the supplier order. |
| **id** string, required | Unique external identifier for an item in the supplier order. |
| **use_supplier_order_id** string, optional | Dictates whether to use the supplier-supplied order id. By default, `use_supplier_order_id` is set to `false`. |
| **order_item_status** string, required | Possible values are `pending`, `processing`, `sent`, `delivered`, `rejected`, `returned`, or `canceled`. |
| **status_notes** string, optional | Notes that further describe details pertaining to the status |
| **status_reason** string, optional | Reason code to describe the status. Possible values are `created`, `backorder`, `preorder`, `special order`, `order acknowledged`, `backorder released`, `preorder released`, `special order released`, `awaiting fulfillment`, `awaiting pickup`, `sent`, `item received`, `customer pickup`, `out of stock`, `member cap internal`, `segment cap internal`, `program cap internal`, `offer cap internal`, `member cap supplier`, `segment cap supplier`, `program cap supplier`, `offer cap supplier`, `canceled`, `cancel requested`, `returned`, or `return initiated`. |
| **carrier_name** string, optional | Name of the shipping carrier. |
| **carrier_tracking** string, optional | Tracking number provided by the shipping carrier. |
| **carrier_url** string, optional | Shipping carrier's tracking url for the item. |
| **carrier_delivery_date** string, optional | Shipping carrier's estimated delivery date. |
| **detailed** boolean, optional | Dictates whether full order details are returned. By default, `detailed` is set to `false`. |
| **adjustment_amount** decimal, optional | The amount to adjust the order total by — a positive amount increases the amount the rewards program owes the supplier, a negative amount decreases the amount the rewards program owers the supplier. |
| **adjustment_description** string, optional | Description of the adjustment. |

##### Endpoint

```nginx
PATCH /api/v4/suppliers/{supplier_id}/orders/{order_id}/order_items/{id}
```
