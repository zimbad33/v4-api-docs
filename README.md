Overview
========

The purpose of this document is to provide detailed descriptions for RewardOps API v3 — calls, parameters and responses. These can be tested using our [interactive API console](https://app.rewardops.net/api_docs/console).


Introduction
============

## Security

### HTTPS

All requests must be performed using SSL via the HTTPS protocol.

### OAuth

RewardOps uses [OAuth 2.0](http://oauth.net/2/) with the Client Credentials grant type. This grant type requires you to use your `client_id` and `client_secret` to create a temporary access token, which is sent with subsequent API requests.

#### Generating an access token

To get an access token, send a POST request to `https://app.rewardops.net/api/v3/auth/token`. The request must have an `Authorization` field in the header. The value of the field must be `Basic $ENCODED_CREDENTIALS`, where `$ENCODED_CREDENTIALS` is your `client_id` and `client_secret`, separated by a colon (`client_id:client_secret`) and base64 encoded.

The JSON response will contain an `access_token` that you can use in subsequent requests. Tokens are valid for two hours. When your token has expired, you'll need to request another token before making further API calls. The API server will respond with an error when you make a request using an expired or otherwise invalid token.

##### Example

```nginx
# Base64-encode the client_id and client_secret, separated by a colon. Then use the base64 string in the Authorization header.

echo -n "CLIENT_ID:CLIENT_SECRET" | openssl enc -base64 -A | { read ENCODED_CREDENTIALS; curl https://demo.rewardops.net/api/v3/auth/token --header "Authorization: Basic $ENCODED_CREDENTIALS" --data "grant_type=client_credentials" --verbose; }

# Response:

{"access_token":"ac7523d4011492d83bfa1ecd5f62ec3c3a1962e1fee84fa53305fc7c75d29c8b","token_type":"bearer","expires_in":7200,"created_at":1428705205}
```

#### Using access tokens

Once you've generated an access token, you can use it to make requests to the API. All API requests must have an `Authorization` field in the header. The value of the field must be `Bearer $ACCESS_TOKEN`, where `$ACCESS_TOKEN` is the valid access token that you created.

## Versioning

Different versions of the API have different base URLs. The base URL for API v3 is `https://app.rewardops.net/api/v3`.

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

## Programs

This set of endpoints facilitates the the retrieval of details related to your rewards programs.

- **List all programs**
- **Retrieve a program**

## Rewards

This set of endpoints facilitates the navigation of the rewards catalog by an end user.

- **List all rewards**
- **Retrieve a reward**

## Orders

This set of endpoints facilitates the placement of an order by a rewards program on behalf of an end user.

- **Create an order**
- **List all orders**
- **Retrieve an order**

## Brands

This set of endpoints facilitates the the retrieval of details related to your brands.

- **List all brands**
- **Retrieve a brand**

## Brand Orders

This set of endpoints facilitates the placement of an order by a brand on behalf of an end user.

- **Create a brand order**
- **Retrieve a brand order**
- **Update a brand order**
- **List all brand orders**


Programs
========

## The program object

| Attributes |   |
|------------|---|
| **id** integer | Unique identifier assigned to each program |
| **active** boolean | If the program is active or inactive |
| **name** string | Name of the rewards program |
| **decription** string | Short description of the rewards program |
| **url** string | Website for the rewards program |
| **network_commission** decimal | RewardOps' transaction fee per reward ordered |
| **currency_name** string | Name of your reward currency (eg. Points, Miles, Coins) |
| **created_at** date | Date rewards program was created |
| **updated_at** date | Date rewards program was last updated |

##### Example Response

```json
{
  "id": 2,
  "active": true,
  "name": "SaveUp",
  "description": "Get rewarded for your savings, not your spending. A revolutionary free rewards program to help people save money and get out of debt.",
  "url": "https://www.saveup.com",
  "network_commission": null,
  "currency_name": "Points",
  "created_at": "2014-04-01T14:53:09.000Z",
  "updated_at": "2014-09-09T16:47:05.000Z"
}
```

## List all programs

Returns a list of reward programs owned by your company.

| Arguments |   |
|-----------|---|
| **page** tag, optional | The page number returned |
| **per_page_count** integer, optional | Records returned per page |

##### Endpoint

```nginx
GET /api/v3/programs
```

## Retrieve a program

Retrieves the details of a rewards program owned by your company.

| Arguments |   |
|-----------|---|
| **program_id** integer, required | Unique identifier assigned to each rewards program |

##### Endpoint

```nginx
GET /api/v3/programs/{program_id}
```


Rewards
=======

Items which will appear in your rewards catalog are stored in reward objects, and reward objects are composed of several sub-objects: Program Value, Retail Value, Brand, Offer Fulfillment, Offer Setting, Creatives, Geotargeting, and Custom Parameters. Rewards may be physical goods (to be shipped) or digital.

## The reward object

| Attributes |   |
|------------|---|
| **id** integer | Unique identifier assigned to each reward |
| **current_status** string | Reward status (scheduled, running, paused, stopped, expired) |
| **program_value** object | A [program value object](#program-value) for the reward |
| **quantity_remaining** integer | The lesser of either global inventory remaining, program inventory remaining or member inventory remaining |
| **starts_at** date | Reward start date |
| **ends_at** date | Reward end date |
| **updated_at** date | The last time the reward was updated |
| **offsite_url** string | URL to access rewards that are redeemed offsite |
| **name** string | The name of the reward the brand is offering (ex $5 gift card) |
| **description** string | A description of the reward |
| **retail_value** object | A [retail value object](#retail-value) for the reward |
| **offer_type** string | The format of the reward (ex Dollar off, Gift Card, Free Product) |
| **offer_sub_type** string | The reward sub-format (ex Dollar off w/ Co-spend, Dollar off w/ product purchase) |
| **private_offer** boolean | Is the reward internal or from a marketplace partner |
| **geo_targeting** object | Relevant countries and sub-regions for the reward |
| **brand** object | Details relating to the brand that is providing the reward |
| **offer_fulfillment** object | Details relating to reward fulfillment |
| **offer_setting** object | Details relating to reward terms and conditions |
| **creatives** object | Image links for the reward |
| **custom_parameters** array | Returns any custom properties created by the program |

##### Example reward object

```json
{
  "id": 1,
  "current_status": "Running",
  "program_value": {
    "fixed_value": null,
    "retail_value_conversion": "0.02"
  },
  "quantity_remaining": "928",
  "starts_at": "2015-02-26T00:00:00.000Z",
  "ends_at": "2016-02-26T23:59:00.000Z",
  "updated_at": "2015-02-26T13:20:23.000Z",
  "offsite_": null,
  "name": "Gift Cards from $1 to $100",
  "description": "Amazon.com Gift Cards never expire and can be redeemed towards millions of items at www.amazon.com.",
  "retail_value": {
    "fixed_value": null,
    "min_value": "1.00",
    "max_value": "100.00"
  },
  "offer_type": "GIFT_CARD",
  "offer_sub_type": null,
  "private_offer": false,
  "geo_targeting": {
    "US": [
      "ALL"
    ]
  },
  "brand": {
    "name": "Amazon.com",
    "description": "Amazon.com is the place to find and discover almost anything you want to buy online at a great price.",
    "url": "http://www.amazon.com/",
    "image_url": "https://rwrd.ps/8D9wSks9"
  },
  "offer_fulfillment": {
    "member_form_fields": [
      "id"
    ],
    "fulfillment_instructions": null
  },
  "offer_setting": {
    "terms_and_conditions": "",
    "member_cap_amount": null,
    "member_cap_interval": null,
  },
  "creatives": {
    "normal": "https://rwrd.ps/8D9wSks9",
    "large": "https://rwrd.ps/8D9wSks9",
    "thumb": "https://rwrd.ps/8D9wSks9"
  },
  "custom_parameters": [{
    "name": "Category",
    "value": "Gift Cards"
  }],
}
```

### The program value sub-object

| Attributes |   |
|------------|---|
| **fixed_value** integer | If the reward has a fixed program value, the amount of points/currency required to order it |
| **retail_value_conversion** decimal | If the reward has a variable program value, the conversion rate of points/currency to retail value |

### The retail value sub-object

| Attributes |   |
|------------|---|
| **fixed_value** decimal | If the reward has a fixed retail value, the dollar value of the reward |
| **min_value** decimal | If the reward has a variable retail value, the minimum retail value |
| **max_value** decimal | If the reward has a variable retail value, the maximum retail value |

### The geo targeting sub-object

| Attributes |   |
|------------|---|
| **US** array | Applicable states within the US. Returns ‘All’ if nationwide availability. |
| **CA** array | Applicable provinces within Canada. Returns ‘All’ if nationwide availability |

### The brand sub-object

| Attributes |   |
|------------|---|
| **name** string | Name of the Brand providing the reward |
| **description** string | Description of the Brand providing the reward |
| **url** string | URL for the Brand providing the reward |
| **image_url** string | Logo for the Brand providing the reward |

### The offer fulfillment sub-object

| Attributes |   |
|------------|---|
| **delivery** string | Delivery method which is either digital (instant delivery) or offline (delayed delivery) |
| **member_form_fields** array | Member information required to complete the order (Name, Email, Address, and/or mobile number) |
| **fulfillment_instructions** string | Details pertaining to the delivery of the delivery of the reward |

### The offer setting sub-object

| Attributes |  |
|------------|--|
| **terms_and_conditions** string | Specific terms and conditions related to the usage of a reward |
| **member_cap_amount** integer | Limit related to the amount of times a member can order a reward |
| **member_cap_interval** string | Time interval related to when the member cap resets (day, week, month, year, lifetime) |
| **global_remaining** integer | Amount of orders remaining for the reward |

### The creative sub-object

| Attributes |   |
|------------|---|
| **normal** string | URL for Reward creative (180x150) |
| **large** array | URL for Reward creative (600x500) |
| **thumb** string | URL for Reward creative (90x75) |

### The custom parameter sub-object

| Attributes |   |
|------------|---|
| **name** string | Name of the custom property |
| **value** variable | Value associated with the custom property. Data type varies depending on the custom parameter being sent |

## List all rewards

Returns a list of your rewards. The rewards are returned sorted by creation date, with the most recently created rewards appearing first.

| Arguments |   |
|-----------|---|
| **program_id** integer, required | Unique identifier assigned to each program |
| **detailed** boolean, optional | the type of list returned |
| **member_id** integer, optional | Unique identifier assigned to each member |
| **page** tag, optional | The page number returned |
| **per_page_count** integer, optional | Records returned per page |
| **search** string, optional | Search term for reward and/or brand name |
| **search_property** hash, optional | Query one or more custom properties (eg. search_property[Category]= Games+and+Toys) |
| **sort_by** string, optional | Sort reward results [newest, expiring, or alphabetic] |
| **status** string, optional | Filter by reward status [running, paused, expired] |

##### Endpoint

```nginx
GET /api/v3/programs/{program_id}/rewards
```

## Retrieve a reward

Retrieves the details of a reward.

| Arguments |   |
|-----------|---|
| **reward_id** integer, required | Unique identifier assigned to each reward |
| **member_id** integer, optional | Unique identifier assigned to each member |

##### Endpoint

```nginx
GET /api/v3/programs/{program_id}/rewards/{id}
```


Orders
======

The rewards redemption (by customers) and the corresponding placement of an order (by the rewards programs) for previously defined reward(s) is handled through the creation of order objects. Orders are composed of several sub-objects: Reward, Member, and Assets.

## The order object

| Attributes |   |
|------------|---|
| **order_id** integer | Unique identifier for the order |
| **program_id** integer | Unique identifier for the rewards program that created the order |
| **created_at** date | Date the order was created |
| **order_status** string | Status of the order (Pending, Processing, Sent, Delivered, Returned, Rejected) |
| **payment_status** string | Payment status of the order (Paid or Refunded) |
| **redemption_percentage** decimal | Percentage of the reward that been redeemed by the member |
| **reward** object | Reward details and fulfillment assets for the order  |
| **member** object | Information pertaining to the member that ordered the reward  |

##### Example order object

```json
{
  "order_id": "2a87bba269b7cf",
  "program_id": 2,
  "created_at": "2014-09-11T01:26:10.000Z",
  "order_status": "DELIVERED",
  "payment_status": "PAID",
  "redemption_percentage": "100.0",
  "reward": {
    "id": 1,
    "current_status": "Running",
    "program_value": {
      "fixed_value": null,
      "retail_value_conversion": "0.02"
    },
    "quantity_remaining": "928",
    "starts_at": "2015-02-26T00:00:00.000Z",
    "ends_at": "2016-02-26T23:59:00.000Z",
    "updated_at": "2015-02-26T13:20:23.000Z",
    "offsite_": null,
    "name": "Gift Cards from $1 to $100",
    "description": "Amazon.com Gift Cards never expire and can be redeemed towards millions of items at www.amazon.com.",
    "retail_value": {
      "fixed_value": null,
      "min_value": "1.00",
      "max_value": "100.00"
    },
    "offer_type": "GIFT_CARD",
    "offer_sub_type": null,
    "private_offer": false,
    "geo_targeting": {
      "US": [
        "ALL"
      ]
    },
    "brand": {
      "name": "Amazon.com",
      "description": "Amazon.com is the place to find and discover almost anything you want to buy online at a great price.",
      "url": "http://www.amazon.com/",
      "image_url": "https://rwrd.ps/8D9wSks9"
    },
    "offer_fulfillment": {
      "member_form_fields": [
        "id"
      ],
      "fulfillment_instructions": null
    },
    "offer_setting": {
      "terms_and_conditions": "",
      "member_cap_amount": null,
      "member_cap_interval": null,
    },
    "creatives": {
      "normal": "https://rwrd.ps/8D9wSks9",
      "large": "https://rwrd.ps/8D9wSks9",
      "thumb": "https://rwrd.ps/8D9wSks9"
    },
    "custom_parameters": [{
      "name": "Category",
      "value": "Gift Cards"
    }],
    "assets": {
      "codes": [{
        "label": "Pin Code",
        "type": "PLAIN",
        "value": "23890fdwu234kljwf",
        "fulfillment": {
          "status": "DELIVERED"
        }
      }],
      "links": [{
        "label": "Website",
        "type": "REDIRECT",
        "value": "https://rwrd.ps/RNArj6a57NOh",
        "fulfillment": {
          "status": "DELIVERED"
        }
      }]
    }
  },
  "member": {
    "id": "234589024",
    "full_name": "John Smith",
    "email": "john@smith.com",
    "phone": "2048675309",
    "address": {
      "address": "string",
      "address_2": "string",
      "city": "string",
      "country_code": "US",
      "country_subregion_code": "NY",
      "postal_code": "10016"
    }
  }
}
```

### The reward sub-object

See [the reward object](#rewards-the-reward-object) above for more details.

### The member sub-object

| Attributes |   |
|------------|---|
| **id** string | Unique identifier assigned to each member of the program |
| **full_name** string | First and last name of the member |
| **email** string | Email address of the member |
| **phone** string | Mobile phone number of the member |
| **address** object | Mailing address of the member |

### The address sub-object

| Attributes |   |
|------------|---|
| **address** string | Address line 1 |
| **address_2** string | Address line 2 |
| **city** string | City |
| **country_code** string | Country [US or CA] |
| **county_subregion_code** string | State or Province |
| **postal_code** string | Postal code or zip code |

### The asset sub-object

| Attributes |   |
|------------|---|
| **codes** array of codes | An array of code assets that are required to redeem a reward.  |
| **links** array of links | An array of link assets that are required to redeem a reward.  |
| **images** array of images | An array of image assets that are required to redeem a reward.  |
| **external** array of external | An array of external assets that will be sent directly to the member by the brand.  |

### The code sub-object

| Attributes |   |
|------------|---|
| **label** string | Name of the asset being delivered. Defined by the brand providing the reward. (eg. Promo Code, Pin Code, Barcode) |
| **type** string | Type of reward asset. PLAIN is currently the only type. |
| **value** string | The code asset, delivered as an alphanumeric code. |
| **fulfillment** object | Fulfillment details related to the reward asset |

### The link sub-object

| Attributes |   |
|------------|---|
| **label** string | Name of the asset being delivered. Defined by the brand providing the reward. (eg. Website, Magazine, MP3, eBook) |
| **type** string | Type of reward asset. Options for external asset types include DOWNLOAD and REDIRECT. |
| **value** string | The link asset, delivered as a URL. |
| **fulfillment** object | Fulfillment details related to the reward asset |

### The image sub-object

| Attributes |   |
|------------|---|
| **label** string | Name of the asset being delivered. Defined by the brand providing the reward. (eg. Barcode, Certificate, Coupon) |
| **type** string | Type of reward asset. PLAIN is currently the only type. |
| **value** string | The image asset, delivered as a URL. |
| **fulfillment** object | Fulfillment details related to the reward asset |

### The external sub-object

| Attributes |   |
|------------|---|
| **label** string | Name of the reward asset. Defined by the brand providing the reward. (eg. Gift card, Merchandise, Magazine) |
| **type** string | Type of reward asset. Options for external asset types include MAILED and EMAIL. |
| **value** string | Not applicable for External assets as they are delivered by the brand |
| **fulfillment** object | Fulfillment details related to the reward asset |

### The fulfillment sub-object

| Attributes |   |
|------------|---|
| **status** string | Status of the asset (Pending, Processing, Sent, Delivered, Returned, Rejected) |
| **carrier_name** string | Name of the mail carrier. Only applicable for type = MAILED. |
| **carrier_tracking** string | Tracking number provided by the mail carrier. Only applicable for type = MAILED. |

## Create an order

Creates a new order object, specific to rewards programs.

| Arguments |   |
|-----------|---|
| **program_id** integer, required | Unique identifier assigned to each program |
| **reward_id** integer, required | Unique identifier assigned to each reward |
| **retail_value** decimal, optional | Dollar value of the reward |
| **member** object, required | JSON representation of the member ordering the reward |

##### Endpoint

```nginx
POST /api/v3/programs/{program_id}/orders
```

## List all orders

Returns a list of orders for a specific member in your rewards program. The orders are returned sorted by reverse chronological order.

| Arguments |   |
|-----------|---|
| **member_id** integer, required | Unique identifier assigned to each member |
| **program_id** integer, required | Unique identifier assigned to each program |
| **detailed** boolean, optional | The type of list returned |
| **redeemed** boolean, optional | Return orders which have/have not been fully redeemed |
| **private** boolean, optional | Return orders with private/non-private offers |
| **payment_status** string, optional | Filter by payment status [paid, refunded] |
| **fulfillment_status** string, optional | Filter by fulfillment status [complete, pending, stopped] |
| **page** tag, optional | The page number returned |
| **per_page_count** integer, optional | Records returned per page |
| **search** string, optional | Search term for reward and/or brand name |
| **sort_by** string, optional | Sort reward results [newest, oldest] |

##### Endpoint

```nginx
GET /api/v3/programs/{program_id}/orders
```

## Retrieve an order

Retrieves the details of an existing order, specific to rewards programs.

| Arguments |   |
|-----------|---|
| **order_id** integer, required | Unique identifier for the order |
| **program_id** integer, required | Unique identifier assigned to each program |

##### Endpoint

```nginx
GET /api/v3/programs/{program_id}/orders/{id}
```


Brands
======

## The brand object

| Attributes |   |
|------------|---|
| **id** integer | Unique identifier assigned to each brand |
| **active** boolean | If the brand is active or inactive |
| **name** string | Name of the brand |
| **decription** string | Short description of the brand |
| **url** string | Website for the brand |
| **image_url** string | Creative for the brand |
| **created_at** date | Date brand was created |
| **updated_at** date | Date brand was last updated |

##### Example brand object

```json
{
  "id": 2,
  "active": true,
  "name": "DonorsChoose.org",
  "description": "DonorsChoose.org is an online charity that makes it easy to help students in need through school donations.",
  "url": "https://www.donorschoose.org",
  "image_url": "https://rwrd.ps/98dfs9ds8",
  "created_at": "2014-04-01T14:53:09.000Z",
  "updated_at": "2014-09-09T16:47:05.000Z"
}
```

## List all brands

Returns a list of reward programs owned by your company.

| Arguments |   |
|-----------|---|
| **page** tag, optional | The page number returned. |
| **per_page_count** integer, optional | Records returned per page. |

##### Endpoint

```nginx
GET /api/v3/brands
```

## Retrieve a brand

Retrieves the details of a brand.

| Arguments |   |
|-----------|---|
| **brand_id** integer, required | The unique identifier assigned to each brand. |

##### Endpoint

```nginx
GET /api/v3/brands/{id}
```


Brand Orders
============

The placement of an order by a brand, on behalf of an end user in a rewards program, is handled through the creation of brand order objects. Brand orders are composed of several sub-objects: Reward, Member, and Assets.

Brand orders are comprised of several sub-objects: Reward, Member, and Assets.

## The brand order object

| Attributes |   |
|------------|---|
| **order_id** integer | Unique identifier for the order |
| **brand_id** integer | Unique identifier for the brand that created the order |
| **created_at** date | Date the order was created |
| **order_status** string | Status of the order (Pending, Processing, Sent, Delivered, Returned, Rejected)
| **payment_status** string | Payment status of the order (Paid or Refunded) |
| **redemption_percentage** decimal | Percentage of the reward that been redeemed by the member |
| **reward** object | Reward details and fulfillment assets for the order  |
| **member** object | Information pertaining to the member that ordered the reward |

### The reward sub-object

| Attributes |   |
|------------|---|
| **id** integer | Unique identifier assigned to each reward |
| **current_status** string | Reward status (scheduled, running, paused, stopped, expired) |
| **program_value** object | A [program value object](#program-value) for the reward |
| **quantity_remaining** integer | The lesser of either global inventory remaining, program inventory remaining or member inventory remaining |
| **starts_at** date | Reward start date |
| **ends_at** date | Reward end date |
| **updated_at** date | The last time the reward was updated |
| **offsite_url** string | URL to access rewards that are redeemed offsite |
| **name** string | The name of the reward the brand is offering (ex $5 gift card) |
| **description** string | A description of the reward |
| **retail_value** object | A [retail value object](#retail-value) for the reward |
| **offer_type** string | The format of the reward (eg. Dollar off, Gift Card, Free Product) |
| **offer_sub_type** string | The reward sub-format (eg. Dollar off w/ Co-spend, Dollar off w/ product purchase) |
| **private_offer** boolean | Is the reward internal or from a marketplace partner |
| **geo_targeting** object | Relevant countries and sub-regions for the reward |
| **brand** object | Details relating to the brand that is providing the reward |
| **offer_fulfillment** offer_fulfillment | Details relating to reward fulfillment |
| **offer_setting** object | Details relating to reward terms and conditions |
| **creatives** object | Image links for the reward |
| **custom_parameters** array | Returns any custom properties created by the program |
| **assets** object | Assets contain information about some of the goods or services that the member has received (or will receive) as part of the reward order.  |

### The asset sub-object

| Attributes |   |
|------------|---|
| **codes** array of codes | An array of code assets that are required to redeem a reward.  |
| **links** array of links | An array of link assets that are required to redeem a reward.  |
| **images** array of images | An array of image assets that are required to redeem a reward.  |
| **external** array of external | An array of external assets that will be sent directly to the member by the brand.  |

### The code sub-object

| Attributes |   |
|------------|---|
| **label** string | Name of the asset being delivered. Defined by the brand providing the reward. (eg. Promo Code, Pin Code, Barcode) |
| **type** string | Type of reward asset. PLAIN is currently the only type. |
| **value** string | The code asset, delivered as an alphanumeric code. |
| **fulfillment** object | Fulfillment details related to the reward asset |

### The links sub-object

| Attributes |   |
|------------|---|
| **label** string | Name of the asset being delivered. Defined by the brand providing the reward. (eg. Website, Magazine, MP3, eBook) |
| **type** string | Type of reward asset. Options for external asset types include DOWNLOAD and REDIRECT. |
| **value** string | The link asset, delivered as a URL. |
| **fulfillment** object | Fulfillment details related to the reward asset |

### The image sub-object

| Attributes |   |
|------------|---|
| **label** string | Name of the asset being delivered. Defined by the brand providing the reward. (eg. Barcode, Certificate, Coupon) |
| **type** string | Type of reward asset. PLAIN is currently the only type. |
| **value** string | The image asset, delivered as a URL. |
| **fulfillment** object | Fulfillment details related to the reward asset |

### The external sub-object

| Attributes |   |
|------------|---|
| **label** string | Name of the reward asset. Defined by the brand providing the reward. (eg. Gift card, Merchandise, Magazine) |
| **type** string | Type of reward asset. Options for external asset types include MAILED and EMAIL. |
| **value** string | Not applicable for External assets as they are delivered by the brand |
| **fulfillment** object | Fulfillment details related to the reward asset |

### The fulfillment sub-object

| Attributes |   |
|------------|---|
| **status** string | Status of the asset (Pending, Processing, Sent, Delivered, Returned, Rejected) |
| **carrier_name** string | Name of the mail carrier. Only applicable for type = MAILED. |
| **carrier_tracking** string | Tracking number provided by the mail carrier. Only applicable for type = MAILED. |

### The member sub-object

| Attributes |   |
|------------|---|
| **id** string | Unique identifier assigned to each member of the program |
| **full_name** string | First and last name of the member |
| **email** string | Email address of the member |
| **phone** string | Mobile phone number of the member |
| **address** address | Mailing address of the member |

### The address sub-object

| Attributes |   |
|------------|---|
| **address** string | Address line 1 |
| **address_2** string | Address line 2 |
| **city** string | City |
| **country_code** string | Country [US or CA] |
| **county_subregion_code** string | State or Province |
| **postal_code** string | Postal code or zip code |

##### Example brand order object

```json
{
  "order_id": "2a87bba269b7cf",
  "program_id": 2,
  "created_at": "2014-09-11T01:26:10.000Z",
  "order_status": "DELIVERED",
  "payment_status": "PAID",
  "redemption_percentage": "100.0",
  "reward": {
    "id": 1,
    "current_status": "Running",
    "program_value": {
      "fixed_value": null,
      "retail_value_conversion": "0.02"
    },
    "quantity_remaining": "928",
    "starts_at": "2015-02-26T00:00:00.000Z",
    "ends_at": "2016-02-26T23:59:00.000Z",
    "updated_at": "2015-02-26T13:20:23.000Z",
    "offsite_": null,
    "name": "Gift Cards from $1 to $100",
    "description": "Amazon.com Gift Cards never expire and can be redeemed towards millions of items at www.amazon.com.",
    "retail_value": {
      "fixed_value": null,
      "min_value": "1.00",
      "max_value": "100.00"
    },
    "offer_type": "GIFT_CARD",
    "offer_sub_type": null,
    "private_offer": false,
    "geo_targeting": {
      "US": [
        "ALL"
      ]
    },
    "brand": {
      "name": "Amazon.com",
      "description": "Amazon.com is the place to find and discover almost anything you want to buy online at a great price.",
      "url": "http://www.amazon.com/",
      "image_url": "https://rwrd.ps/8D9wSks9"
    },
    "offer_fulfillment": {
      "member_form_fields": [
        "id"
      ],
      "fulfillment_instructions": null
    },
    "offer_setting": {
      "terms_and_conditions": "",
      "member_cap_amount": null,
      "member_cap_interval": null,
    },
    "creatives": {
      "normal": "https://rwrd.ps/8D9wSks9",
      "large": "https://rwrd.ps/8D9wSks9",
      "thumb": "https://rwrd.ps/8D9wSks9"
    },
    "custom_parameters": [{
      "name": "Category",
      "value": "Gift Cards"
    }],
    "assets": {
      "codes": [{
        "label": "Pin Code",
        "type": "PLAIN",
        "value": "23890fdwu234kljwf",
        "fulfillment": {
          "status": "DELIVERED"
        }
      }],
      "links": [{
        "label": "Website",
        "type": "REDIRECT",
        "value": "https://rwrd.ps/RNArj6a57NOh",
        "fulfillment": {
          "status": "DELIVERED"
        }
      }]
    }
  },
  "member": {
    "id": "234589024",
    "full_name": "John Smith",
    "email": "john@smith.com",
    "phone": "2048675309",
    "address": {
      "address": "string",
      "address_2": "string",
      "city": "string",
      "country_code": "US",
      "country_subregion_code": "NY",
      "postal_code": "10016"
    }
  }
}
```

## Create a brand order

Creates a new brand order object.

| Arguments |   |
|-----------|---|
| **brand_id** integer, optional | Unique identifier assigned to each brand |
| **offer_id** integer, required | Unique identifier assigned to each offer |
| **program_id** integer, required | Unique identifier assigned to each program |
| **retail_value** decimal, optional | Dollar value of the reward |
| **points_debit** integer, required | Currency/Points  |
| **member** member, required | JSON representation of the member ordering the reward |
| **assets** object, required | JSON representation of the assets associated with the order |

##### Endpoint

```nginx
POST /api/v3/brands/orders
```

## Retrieve a brand order

Retrieves the details of an existing brand order.

| Arguments |   |
|-----------|---|
| **order_id** integer, required | Unique identifier for the order |
| **brand_id** integer, optional | Unique identifier assigned to each brand |

##### URL

```nginx
GET /api/v3/brands/orders/{id}
```

## Update a brand order

Updates the specific brand order by setting the values of the parameters passed. 

| Arguments |   |
|-----------|---|
| **brand_id** integer, optional | Unique identifier assigned to each brand |
| **order_id** integer, required | Unique identifier assigned to each order |
| **redemption_percentage** decimal, optional | Percentage of the reward that been redeemed by the member |
| **assets** assets, optional | JSON representation of the assets associated with the order |

##### URL

```nginx
PATCH /api/v3/brands/orders/{id}
```

## List all brand orders

Returns a list of brand orders for a specific program, offer, and/or brand.

| Arguments |   |
|-----------|---|
| **brand_id** integer, optional | Unique identifier assigned to each brand |
| **offer_id** integer, optional | Unique identifier assigned to each offer |
| **program_id** integer, required | Unique identifier assigned to each program |
| **detailed** boolean, optional | The type of list returned |
| **redeemed** boolean, optional | Return orders which have/have not been fully redeemed |
| **private** boolean, optional | Return orders with private/non-private offers |
| **payment_status** string, optional | Filter by payment status [paid, refunded] |
| **fulfillment_status** string, optional | Filter by fulfillment status [complete, pending, stopped] |
| **page** tag, optional | The page number returned |
| **per_page_count** integer, optional | Records returned per page |
| **search** string, optional | Search term for reward and/or brand name |
| **sort_by** string, optional | Sort reward results [newest, oldest] |

##### Endpoint

```nginx
GET /api/v3/brands/orders
```
