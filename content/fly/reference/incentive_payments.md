---
title: Incentive Payments
weight: 9
---

## Payment - Reloadly

JSON:
``` json
{
    "type": "wait",
    "wait": {
        "type": "external",
        "value": {
            "type": "payment:reloadly",
            "id": "PAYMENT_ID"
        }
    },
    "payment": {
        "provider": "reloadly",
        "key": "name-of-your-credentials",
        "details": {
            "mobile": @MOBILE_QUESTION,
            "operator": @OPERATOR_QUESTION,
            "amount": 100,
            "tolerance": 30,
            "country": "IN",
            "id": "PAYMENT_ID",
            "custom_identifier": "CUSTOM_IDENTIFIER",
        }
    }
}
```

Notes:

1. The "wait" is not strictly necessary but likely desired!
2. `PAYMENT_ID` can be useful to keep track of multiple payments to the same person or different payments to different treatment arms (a unique id per treatment arm). You need to have the same PAYMENT_ID for both the "wait" and the "payment" blocks.
3. the `key` is the name you give the desired Reloadly credentials in the Fly dashboard.
4. `CUSTOM_IDENTIFIER` is a unique string that ensures that no payment is repeated. For example, if you only want to provide a payment to each phone once, you can make this a combination of the shortcode and the phone number.

You will have the following hidden fields that can be used for logic and error messages:

1. `e_payment_reloadly_success` - will be "true" if the payment succeeded.
2. `e_payment_reloadly_error_message` - an error message, in english, of why the payment failed.
3. `e_payment_reloadly_id` - the PAYMENT_ID



## Payment - Generic HTTP Payment Endpoint

This allows you to send payments to an external API via any http request.

JSON:
``` json
{
    "type": "wait",
    "wait": {
        "type": "external",
        "value": {
            "type": "payment:http",
            "id": "PAYMENT_ID"
        }
    },
    "payment": {
        "provider": "http",
        "details": {
            "id": "PAYMENT_ID",
            "method": "POST",
            "url": "https://mypaymentprovider.com/send/money",
            "headers": {"Authorization": "Bearer << MYPROVIDER_TOKEN >>"},
            "body": { "phone": "@MOBILE_QUESTION", "amount": 100, "transaction_id": "survey_x_payment_1" },
            "errorMessage": "path.to.error.message"
        }
    }
}
```

Notes:

1. The "wait" is not strictly necessary but likely desired!
2. `PAYMENT_ID` can be useful to keep track of multiple payments to the same person or different payments to different treatment arms (a unique id per treatment arm).
3. The `body` and `headers` properties are optional.
4. You can pass secrets into the url, the headers, and/or the body. This is done with templating which uses the delimeters `<<` and `>>`. The secrets available are the secrets you create in the dashboard under "Generic Secrets".
5. `errorMessage` is a "json path", in dot notation, to extract the message provided in `e_payment_http_error_message`. If the status code is not 2XX, the service will consider it an error and expect a JSON body response. If the body is `{"error": {"code": "BAD_NUMBER", "message": "Please provide a valid mobile number"}}` then the `errorMessage` property should be `error.message` in order to extract the message "Please provide a valid mobile number".
6. If your HTTP payment endpoint requires the phone number as a string, make sure to wrap the reference to the previous question in quotes (`""`).


You will have the following hidden fields that can be used for logic and error messages:

1. `e_payment_http_success` - will be "true" if the payment succeeded.
2. `e_payment_http_error_message` - an error message, extracted as specified from error json.
3. `e_payment_http_id` - the PAYMENT_ID
4. `e_payment_http_response` - the response from the payment API.



## Payment - Tremendous

We can use our Generic HTTP Payment Endpoint type to make a request to [Tremendous](https://tremendous.com) for giving out gift cards.

First, you will need to create a new "Generic Secret" under Connected Accounts on the home screen of the dashboard. Give your secret a variable name of "TREMENDOUS_API_KEY" and a value equal to the API key of your Tremendous account.

Second, you will use the following JSON. Replace `product_id_that_you_want` with the product_id from Tremendous and replace `your_funding_source_id` with the funding source id from Tremendous. You can read how to get your funding source id from the api [here](https://developers.tremendous.com/docs/paying-for-orders#funding-sources). NOTE: DO NOT REPLACE "TREMENDOUS_API_KEY", that is a variable that will be filled with the value from "Generic Secrets" in the Fly dashboard. This ensures that you don't put your secret API KEY in the survey, which will be shared with your collaborators and potentially made public.

JSON:
``` json

{
  "type": "wait",
  "wait": {
    "type": "external",
    "value": {
      "type": "payment:http",
      "id": "giftcard_1"
    }
  },
  "payment": {
    "provider": "http",
    "details": {
      "id": "giftcard_1",
      "method": "POST",
      "url": "https://www.tremendous.com/api/v2/orders",
      "headers": {
        "Authorization": "Bearer << TREMENDOUS_API_KEY >>",
        "Content-Type": "application/json"
      },
      "body": {
        "external_id": "{{hidden:id}}_my_shortcode"      
        "payment": { "funding_source_id": "your_funding_source_id" },
        "rewards": [{
          "value": { "denomination": 5.0, "currency_code": "EUR" },
          "delivery": { "method": "LINK" },
          "recipient": { "name": "Study Participant" },
          "products": ["product_id_that_you_want"]
        }]
      },
      "errorMessage": "errors.message",
      "responsePath": "order.rewards.0.delivery.link|@tostr"
    }
  }
}
```
