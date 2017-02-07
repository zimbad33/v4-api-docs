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

API Endpoints
=============

## Items

## Supplier Items

## Orders

## Supplier Orders

Items
=====

Items which will appear in your rewards catalog are stored in item objects, and item objects are composed of item variant objects along with several sub-objects: Categories, Brands, Properties, Variant Properties, and Images.

## The item variant object

| Attributes |   |
|------------|---|
| **id** integer | Unique identifier assigned to each item |
| **type** string | Item type (Merchandise, Gift Card, Periodical, Donation, Event, Travel, Music, Video, Book, Discount, Offsite) |
| **name** string | Item name |
| **model** string | Manufacturer item model number |
| **industry_id** string | Universal product code (UPC) |
| **description** string | Item description |
| **specifications** string | Item specifications |
| **notes** string | Item notes |
| **related_items** array | A collection of item ids that are similar to the originating item id |
| **categories** object | A [categories object](#categories) for the item |
| **brands** object, optional | A [brands object](#brands) for the item |
| **properties** object, optional | A [properties object](#properties) for the item |
| **variant_properties** object, optional | A [variant properties object](#variant-properties) for the item |
| **images** object | An [images object](#images) for the item |

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

### The categories sub-object

| Attributes |   |
|------------|---|
| **path** string | The full path of the item's categories and sub-categories |
| **code** string | Unique identifier assigned to each category |
| **labels** string | Labels associated with the items category |

### The brands sub-object

| Attributes |   |
|------------|---|
| **name** string | Name of the brand |
| **url** string | Url of the brand |
| **image_url** string | Url of the brand logo |

### The properties sub-object

| Attributes |   |
|------------|---|
| **name** string | Property name |
| **key** string | Property key |
| **value** string | Property value |

### The variant properties sub-object

| Attributes |   |
|------------|---|
| **name** string | Variant property name |
| **key** string | Variant property key |
| **value** string | Variant property value |
| **other_variants** hash, optional | A collection of item ids and names for variant properties |

### The images sub-object

| Attributes |   |
|------------|---|
| **name** string | Name of image |
| **description** string | Short description of image |
| **350x350** string | Url of the image with a resolution of 350x350 |
| **74x74** string | Url of the image with a resolution of 74x74 |
| **46x46** string | Url of the image with a resolution of 46x46 |


