Orders
======

The placement of an order (by a rewards programs or a supplier, on behalf of an end user) is handled through the creation of order objects. Orders are composed of several sub objects: Member, Order Supplier, and Order Item.

## order object

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

### Order Item Payment Statuses

| Payment Status |   |
|------------|---|
| **preflight**  | order intiated but not confirmed by the member |
| **pending**  | order is created, payment is pending review from program |
| **paid**  | order is created, payment has been accepted by program |
| **rejected**  | payment has been rejected by program |
| **failed**  | order payment failed |
| **cancelled**  | order payment has been cancelled by program |
| **refunded**  | order payment has been refunded by program |

### Order Item Fulfillment Statuses

| Fulfillment Status | Status Reasons   |
|------------|---|
| **pending**  | hold <br/> created <br/> backorder <br/> preorder <br/> special_order    |
| **cancelled**  | cancelled <br/> insufficient_escrow <br/> fraud |
| **processing**  | order_acknowledged <br/> order_dispatched <br/>  backorder_released <br/> preorder_released <br/> special_order_released <br/> awaiting_fulfillment <br/> awaiting_pickup|
| **sent**  | sent |
| **delivered**  | item_received <br/> customer_pickup |
| **rejected**  | out_of_stock <br/> invalid_address <br/> fraud <br/> member_cap <br/> program_cap <br/> supplier_cap <br/> member_requested |
| **returned**  | convenience <br/> damaged <br/> defective <br/> undeliverable |
| **failed**  | api_error <br/> ftp_error <br/> insufficent_balance <br/> inactive_member |
