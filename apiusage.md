# Rage Ground API Info

## Table of Contents

- [Login Info](#login)
- [API Token](#api)
- [Booking](#booking)
  - [API](#booking-api)
  - [Info](#booking-info)
  - [Request](#booking-request)
    - [Rage Room Booking](#booking-request_rage)
    - [Paint Room Booking](#booking-request_paint)
  - [Errors](#booking-errors)
- [Availability](#availability)
  - [API](#availability-api)
  - [Info](#availability-info)
  - [Request](#availability-request)
  - [Response](#availability-response)
- [Inventory](#inventory)
  - [API](#inventory-api)
  - [Info](#inventory-info)
  - [Request](#inventory-request)
  - [Response](#inventory-response)
- [Gift Card / Coupon Check](#gift-coupon)
  - [API](#gift-coupon-api)
  - [Info](#gift-coupon-info)
  - [Request](#gift-coupon-request)
  - [Coupon Response](#gift-coupon-coupon-response)
  - [Gift Card Response](#gift-coupon-gift-card-response)
  - [Errors](#gift-coupon-errors)

## Login Info <a name="login"></a>



## API Token <a name="api"></a>

### Token needs to be included in the Authorization as a Bearer Token

### `73e9e6e01fe0b919845aa3f175c52f22535ed1ecff37a2fc742334213f0f2a27514d62f0a310a3ef6697923756e41f1baed8ccf55b6d3134c11252a2fb5088ddf80d72447479b651e943827ff76cd021995bf2ea0a96f14b0d01f2edd1163bf829c550a4ac77a05ceb63859f9d2c1a4eea62389615d6996bc32a80f13614ead6`

## Booking

### POST `https://strapi.edlabs.io/api/booking/book` <a name="booking-api"></a>

### Info <a name="booking-info"></a>

- Book both gift cards and regular appointments
- Appointments and Gift cards can be processes together or separately
  - Need to be an array of objects inside of the `cart` value
- For now location ids are **5756400 (DTLA)** or **6992579 (West LA)**
- Cart total should be updated with the coupon discount if one was provided
  - Coupon and gift card validity should be checked beforehand
    - Provide gift card number if it has been checked and has a balance greater than zero
    - If no gift card then just send an empty string or don't include it
  - Gift card balance will be updated by the server

### Request <a name="booking-request"></a>

#### Rage Room Booking <a name="booking-request_rage"></a>

```json
{
  "cart": [
    {
      "id": "d495f1b8-4b8c-4e7e-a65a-4ebbdb81692c",
      "service": "Rage Room",
      "date": "2022-10-28T13:00:00-0700",
      "items": {
        "Car Smashing": {
          "id": 2985648
        },
        "Beer Bottle": {
          "id": "prod_JlqLxejmfcsOmc",
          "quantity": 6
        },
        "Mug": {
          "id": "prod_JlqMekG3UtIYbI",
          "quantity": 5
        },
        "Keyboard": {
          "id": "prod_JlqNkhwLR5Hl9S",
          "quantity": 5
        },
        "Microwave": {
          "id": "prod_JlqHCny0H1m70Y",
          "quantity": 4
        },
        "Wine Bottle": {
          "id": "prod_JlqKnSPHjUmZuH",
          "quantity": 6
        },
        "Printer - Medium": {
          "id": "prod_JlqIiWqqMGrhgg",
          "quantity": 6
        }
      },
      "people": 4,
      "pkgId": 24420234,
      "location": 6992579
    }
  ],
  "giftCard": "",
  "cartTotal": 384.4,
  "info": {
    "fname": "Rage",
    "lname": "Ground",
    "phone": "2135365356",
    "email": "rage@rageground.com",
    "card": {
      "number": "4242424242424242",
      "exp_month": 5,
      "exp_year": 2033,
      "cvc": "314"
    },
    "postal_code": 90015
  }
}
```

#### Paint Room Booking <a name="booking-request_paint"></a>

```json
{
  "cart": [
    {
      "id": "d495f1b8-4b8c-4e7e-a65a-4ebbdb81692c",
      "service": "Paint Room",
      "date": "2022-10-28T13:00:00-0700",
      "items": {
        "8 Paint Bottles": {
          "quantity": 2
        },
        "Birthday Special (Banner, Balloons, Confetti Popper, T-shirt, Souvenir Bat)": {
          "quantity": 1
        },
        "Birthday Banner": {
          "quantity": 5
        },
        "Car Smashing": {
          "id": 2985648
        }
      },
      "people": 6,
      "pkgId": 36235107,
      "location": 6992579
    }
  ],
  "giftCard": "",
  "cartTotal": 384.4,
  "info": {
    "fname": "Rage",
    "lname": "Ground",
    "phone": "2135365356",
    "email": "rage@rageground.com",
    "card": {
      "number": "4242424242424242",
      "exp_month": 5,
      "exp_year": 2033,
      "cvc": "314"
    },
    "postal_code": 90015
  }
}
```

### Errors <a name="booking-errors"></a>

#### Current booking doesn't have any slots available

```json
{
  "error": "APPT",
  "apptID": "appt.id",
  "message": "Time not available"
}
```

#### Current booking time doesn't have any more slots available for this amount of people

- Will return how many slots are available
- 1 slot is equivalent to 5 people

```json
{
  "error": "APPT",
  "apptID": "appt.id",
  "slotsAvailable": "1",
  "message": "Time not available"
}
```

#### As of booking that time isn't available anymore

```json
{
  "error": "APPT",
  "apptID": "d495f1b8-4b8c-4e7e-a65a-4ebbdb81692c",
  "message": "Time not available"
}
```

#### Insufficient inventory for item

```json
{
  "error": "INV",
  "data": "key",
  "message": "Insufficient inventory"
}
```

#### Error creating the gift card

```json
{
  "error": "GIFT",
  "data": "error",
  "message": "Error creating gift card"
}
```

## Availability

### POST `https://strapi.edlabs.io/api/availability` <a name="availability-api"></a>

### Info <a name="availability-info"></a>

- Checks for availability of X amount of people on a certain day
- If no availability will return an empty array
- Service can be either **paint** or **rage**
- Location can be either **5756400 (DTLA)** or **6992579 (West LA)**

### Request <a name="availability-request"></a>

```json
{
  "service": "paint",
  "location": 6992579,
  "date": "07/07/2022",
  "people": 5
}
```

### Response <a name="availability-response"></a>

```json
{
  "data": [
    {
      "time": "2022-08-26T13:00:00-0700",
      "slotsAvailable": 4
    },
    {
      "time": "2022-08-26T14:30:00-0700",
      "slotsAvailable": 4
    },
    {
      "time": "2022-08-26T16:00:00-0700",
      "slotsAvailable": 4
    },
    {
      "time": "2022-08-26T17:30:00-0700",
      "slotsAvailable": 4
    },
    {
      "time": "2022-08-26T19:00:00-0700",
      "slotsAvailable": 4
    }
  ],
  "pkgId": 36235081,
  "status": true
}
```

## Inventory

### POST `https://strapi.edlabs.io/api/inventory` <a name="inventory-api"></a>

### Info <a name="inventory-info"></a>

- Get all available inventory for either rage room or paint room

### Request <a name="inventory-request"></a>

- Can be either "paint" or "rage"

```json
{
  "service": "paint"
}
```

### Response <a name="inventory-response"></a>

```json
{
  "data": [
    {
      "uuid": "907f754b-9069-43f6-9ef1-2a5d13d76e35",
      "name": "Birthday Special (Banner, Balloons, Confetti Popper, T-shirt, Souvenir Bat)",
      "price": 60,
      "img": "https://files.stripe.com/links/MDB8YWNjdF8xSjg5clZCdmtQZmxMeGd1fGZsX3Rlc3RfTjNZRnVqUDN5Z2NTelhHam1EUW9xTkJO00pXZXJIQm"
    },
    {
      "uuid": "6e57309c-9e56-4731-812e-d6c2b51a255f",
      "name": "Birthday Banner",
      "price": 15,
      "img": "https://files.stripe.com/links/MDB8YWNjdF8xSjg5clZCdmtQZmxMeGd1fGZsX3Rlc3RfSVRiNFI3NGZEQVFGdGwzSUltbVFqeXRP00XB3ExpSG"
    },
    {
      "uuid": "5298c711-8549-430b-a294-03737a2784f4",
      "name": "8 Paint Bottles",
      "price": 12,
      "img": "https://files.stripe.com/links/MDB8YWNjdF8xSjg5clZCdmtQZmxMeGd1fGZsX3Rlc3RfUGFKTDJwRjFhaVNkdnR0cWxYcGpwSlFM00S7Ug4t2Z"
    },
    {
      "uuid": "b4abbe3e-9946-4203-ba0c-8138d2cf4802",
      "name": "Car Smashing",
      "price": "30.00",
      "id": 2985648
    }
  ],
  "status": true
}
```

## Gift Card / Coupon Check <a name="gift-coupon"></a>

### POST `https://strapi.edlabs.io/api/coupon` <a name="gift-coupon-api"></a>

### Info <a name="gift-coupon-info"></a>

- Check the vailidity of coupons and gift cards
- Returns the remaining balance on gift cards and the value/valueType of coupons

### Request <a name="gift-coupon-request"></a>

```json
{
  "code": "gift_card_test"
}
```

#### Test values

- gift_card_test
- coupon_amount_test
- coupon_percent_test

### Coupon Response <a name="gift-coupon-coupon-response"></a>

#### Type can be either "percent" or "amount"

- Percent

```json
{
  "status": true,
  "type": "coupon",
  "valueType": "percent",
  "value": 15
}
```

- Amount

```json
{
  "status": true,
  "type": "coupon",
  "valueType": "amount",
  "value": 25
}
```

### Gift Card Response <a name="gift-coupon-gift-card-response"></a>

```json
{
  "status": true,
  "type": "gift",
  "valueType": "amount",
  "value": 100
}
```

### Errors <a name="gift-coupon-errors"></a>

- Gift card number entered isn't valid

```json
{
  "error": "GIFT/COUPON",
  "data": {
    "code": "test"
  },
  "message": "Code not valid",
  "status": false
}
```

- Gift card number is valid but has a balance of zero

```json
{
  "error": "GIFT/COUPON",
  "data": {
    "code": "test"
  },
  "message": "Zero balance",
  "status": false
}
```

- Coupon is invalid (Either expired or doesn't exist)

```json
{
  "error": "GIFT/COUPON",
  "data": {
    "code": "test"
  },
  "message": "Coupon expired",
  "status": false
}
```
