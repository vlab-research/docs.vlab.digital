---
title: Question Types
weight: 4
---

Fly supports the following question types. Note that for some types, you must add JSON to the "description" in Typeform. This JSON tells the Fly chatbot how to treat the question, sometimes overriding part or everything in Typeform itself for that question.

{{< toc >}}



## Short Text

This is a free text question. The user can type anything and send it in the chat and it will be accepted as valid.

In Typeform, pick "Short Text"

## Multiple Choice

Creates a multiple choice question.

In Typeform, pick "Multiple Choice".

Notes:

1. You can have a maximum of 13 answers.
2. If any answer text is longer than 15 characters, you should use letters A,B,C...M as the answers instead and the question text should be written in the following format:

```
Which region do you live in?
-A. North Central (Middle Belt)
-B. North East
-C. North West
-D. South East
-E. South South (Niger Delta)
-F. South West
```

The `-` and the `.` before and after the letters are optional, but recommended for legibility.

## Number

Number type validates that the user has sent us a number and only a number. To change the error message when a user enters something other than a number, do: ....

In Typeform, pick "Number".


## Statement

A statement is a simple message that you send. The bot will move on to the next question without waiting for a response.

In Typeform, pick "Statement"

## Image

JSON:
```json
{"type": "attachment",
 "keepMoving": true,
 "attachment": {
    "type": "image",
    "url": "https://i.imgur.com/ZSHauqq.png"
 }
}
```

## Video

JSON:
```json
{"type": "attachment",
 "keepMoving": true,
 "attachment": {
    "type": "video",
    "url": "https://url-to-your-video.mp4"
 }
}
```

## Links

It's possible to send a link as a button and allow the users to open it in a Messenger webview:

JSON:
```json
{
  "type": "webview",
  "url": {
    "base": "links.vlab.digital",
    "params": {
      "url": "asiapacific.unwomen.org/en/countries/india"
    }
  },
  "buttonText": "Visit UN Women",
  "extensions": false,
  "keepMoving": true
}
```

To track information about the user, add metadata as query parameters in addition to `url` in the `url` value. For example, you can add the user id by adding `id` with a typeform ref to a "hidden field" called "id" (note, you will probably need to type out the "@id" part inside Typeform so it links it to a hidden field properly):

```json
{
  "type": "webview",
  "url": {
    "base": "links.vlab.digital",
    "params": {
      "url": "asiapacific.unwomen.org/en/countries/india",
      "id": @id
    }
  },
  "buttonText": "Visit UN Women",
  "extensions": false,
  "keepMoving": true
}
```

Set `keepMoving` to `true` if you want the message to act like a "statement" and continue to the next message. Otherwise, you can combine with a "wait" to wait until the user has clicked the link:

JSON:
```json
{
  "type": "webview",
  "url": {
    "base": "links.vlab.digital",
    "params": {
      "url": "asiapacific.unwomen.org/en/countries/india"
    }
  },
  "buttonText": "Visit UN Women",
  "responseMessage": "Click on the button to visit the website",
  "extensions": false,
  "wait": {
    "type": "external",
    "value": {
      "type": "linksniffer:click",
      "url": "https://asiapacific.unwomen.org"
    }
  }
}
```

You can set `extensions` to `true` if the page you are visiting is using Messenger Extensions (i.e. Fly Survey's "Moviehouse" which can be used to watch Vimeo videos and track video-watching events)

## Stitch

When stitching from one form to another, the "stitch" must be a statement:


JSON:
```json
{"type": "stitch",
 "stitch": { "form": "FORM_SHORTCODE" }}
```

Where `FORM_SHORTCODE` is the shortcode of the form you'd like to move to.

## Wait - Timeout

Timeouts allow you to pause your survey and continue it later, creating multi-wave surveys. There are three ways to create a timeout:

### Absolute timeout:

An absolute timeout fires at a certain time for all survey takers. You must write the time in a SQL-recognizable format, using UTC time, like so:

JSON:

```json
{
    "type": "wait",
    "responseMessage": "Please wait!",
    "wait": {
        "type": "timeout",
        "value": {
            "type": "absolute",
            "timeout": "2021-08-01 12:00"
        }
    }
}
```

A relative timeout fires after a certain amount of time has passed for each survey taker. You must write the duration of time to pass in SQL format, like so:

### Relative timeout:


JSON:

```json
{
    "type": "wait",
    "responseMessage": "Please wait!",
    "wait": {
        "type": "timeout",
        "value": {
            "type": "relative",
            "timeout": "2 days"
        }
    }
}
```

Where `value` written as "1 minute" or "2 hours" or "2 days".



### Variable timeout:

Finally, you can create a timeout variable, if you want to be able to adjust the exact timeout length or time later. This is very valuable! Often recommended instead of absolute timeout, just in case. Note you still need to create a `type` which is `absolute` or `relative`:

JSON:

```json
{
    "type": "wait",
    "responseMessage": "Please wait!",
    "wait": {
        "type": "timeout",
        "value": {
            "type": "absolute",
            "variable": "my_timeout_var"
        }
    }
}
```

You then go to the dashboard and under the survey settings (click on the shortcode), under "Timeouts" add a timeout with the name `my_timeout_var`, pick `absolute`, and select the date and time you would like it to fire.



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
            "id": "PAYMENT_ID"
        }
    }
}
```

Notes:

1. The "wait" is not strictly necessary but likely desired!
2. `PAYMENT_ID` can be useful to keep track of multiple payments to the same person or different payments to different treatment arms (a unique id per treatment arm).
3. the `key` is the name you give the desired Reloadly credentials in the Fly dashboard.

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
