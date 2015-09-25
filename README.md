## Voucherify SDK

### Usage

#### Authentication

```javascript
var voucherifyClient = require("voucherify");

var voucherify = voucherifyClient({
    applicationId: "YOUR-APPLICATION-ID-OBTAINED-FROM-CONFIGURATION",
    clientSecretKey: "YOUR-CLIENT-SECRET-KEY-OBTAINED-FROM-CONFIGURATION"
});
```

#### Getting voucher details

In a callback:
```javascript
voucherify.get("v1GiJYuuS", function(error, result) {
    if (error) {
        console.error("Error: %s", error);
        return;
    }

    console.log(result);
});
```

As a promise:
```javascript
voucherify.get("v1GiJYuuS")
    .then(function (result) {
        console.log(result);
    })
    .catch(function (error) {
        console.error("Error: %s", error);
    });
```

Result:
```json
{
    "code": "v1GiJYuuS",
    "campaign": "vip",
    "discount": 10.0,
    "discountType": "PERCENT",
    "expirationDate": "2015-12-31T23:59:59Z",
    "usage": {
        "quantity": 3,
        "usedQuantity": 1,
        "usageEntries": [
            {
                "date": "2015-09-24T06:03:35Z",
                "tracking_id": "GENERATED-OR-PROVIDED-TRACKING-ID"
            }
        ]
    },
    "additionalInfo": ""
}
```

#### Getting voucher usage

In a callback:
```javascript
voucherify.usage("v1GiJYuuS", function(error, result) {
    if (error) {
        console.error("Error: %s", error);
        return;
    }

    console.log(result);
});
```

As a promise:
```javascript
voucherify.usage("v1GiJYuuS")
    .then(function (result) {
        console.log(result);
    })
    .catch(function (error) {
        console.error("Error: %s", error);
    });
```

Result:
```json
{
    "quantity": 3,
    "usedQuantity": 1,
    "usageEntries": [
        {
            "date": "2015-09-24T06:03:35Z",
            "tracking_id": "GENERATED-OR-PROVIDED-TRACKING-ID"
        }
    ]
}
```

#### Using voucher

##### 1. Just by code

In a callback:
```javascript
voucherify.use("v1GiJYuuS", function(error, result) {
    if (error) {
        console.error("Error: %s", error);
        return;
    }

    console.log(result);
});
```

As a promise:
```javascript
voucherify.use("v1GiJYuuS")
    .then(function (result) {
        console.log(result);
    })
    .catch(function (error) {
        console.error("Error: %s", error);
    });
```

Result (voucher details after usage):

```json
{
    "code": "v1GiJYuuS",
    "campaign": "vip",
    "discount": 10.0,
    "discountType": "PERCENT",
    "expirationDate": "2015-12-31T23:59:59Z",
    "usage": {
        "quantity": 3,
        "usedQuantity": 2,
        "usageEntries": [
            {
                "date": "2015-09-24T06:03:35Z",
                "tracking_id": "(tracking_id not set)"
            },
            {
                "date": "2015-09-25T10:34:57Z",
                "tracking_id": "(tracking_id not set)"
            },
        ]
    },
    "additionalInfo": ""
}
```

Error:
```json
{
  "code": 400,
  "message": "voucher expired or quantity exceeded",
  "details": "v1GiJYuuS"
}
```

##### 2. With tracking id

You can provide a tracking id (e.g. your customer's login or a generated id) to the voucher usage request.

```javascript
voucherify.use("v1GiJYuuS", "alice.morgan",
    function(error, result) {
        if (error) {
            console.error("Error: %s", error);
            return;
        }

        console.log(result);
    });
```

```javascript
voucherify.use("v1GiJYuuS", "alice.morgan")
    .then(function (result) {
        console.log(result);
    })
    .catch(function (error) {
        console.error("Error: %s", error);
    });
```

Result: 
```json
{
    "code": "v1GiJYuuS",
    "campaign": "vip",
    "discount": 10.0,
    "discountType": "PERCENT",
    "expirationDate": "2015-12-31T23:59:59Z",
    "usage": {
        "quantity": 3,
        "usedQuantity": 3,
        "usageEntries": [
            {
                "date": "2015-09-24T06:03:35Z",
                "tracking_id": "(tracking_id not set)"
            },
            {
                "date": "2015-09-25T10:34:57Z",
                "tracking_id": "(tracking_id not set)"
            },
            {
                "date": "2015-09-25T12:04:08Z",
                "tracking_id": "alice.morgan"
            },
        ]
    },
    "additionalInfo": ""
}
```

##### 3. With customer profile

You can record a detailed customer profile consiting of an `id` (obligatory), `name`, `email`, `description` and a `metadata` section that can include any data you wish.

```javascript
voucherify.use({
        voucher: "v1GiJYuuS",
        customer: {
            id: "alice.morgan",
            name: "Alice Morgan",
            email: "alice@morgan.com",
            description: "",
            metadata: {
                locale: "en-GB",
                shoeSize: 5,
                favouriteBrands: ["Armani", "L’Autre Chose", "Vicini"]
            }
        })
    .then(function (result) {
        console.log(result);
    })
    .catch(function (error) {
        console.error("Error: %s", error);
    });
```

### Changelog

- **2015-09-25** - `1.1.2` - Ability to track a detailed customer profile that uses a voucher.
- **2015-09-11** - `1.1.1` - Updated backend URL.
- **2015-08-13** - `1.1.0` - Ability to track use voucher operation.
  - Properly handling voucher codes with not URL-friendly characters.
- **2015-07-09** - `1.0.1` - Returning to old API URL.
- **2015-07-03** - `1.0.0` - Switching API URL.
- **2015-07-03** - `0.2.0` - Adding promises support.
  - You can either:
    - Pass a callback in order to use *old-school* API style.
    - Or you can skip the callback and use returned promise.
- **2015-07-03** - `0.1.1` - Publishing package in the `npm` repository.
- **2015-07-02** - `0.1.0` - First version:
  - Authentication
  - Voucher informations: *get*, *usage*
  - Voucher operations: *use*
