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

For an image you can use a URL:

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

Or, to be more performant, you can pre-upload it to Facebook and then use it as an attachment id:

```json

{"type": "attachment",
 "keepMoving": true,
 "attachment": {
    "type": "image",
    "attachment_id": "3656576331230635"
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


## Upload

The "Upload" type allows users to send attachments, such as images or files. Use the attachment type ("image" for pictures) to specify what kind of file you would like the user to provide. The response in the exported dataset will equal a facebook URL to the file for download.

JSON:
```json
{"type": "upload",
 "upload": {
    "type": "image"
 }
}
```

## Links

It's possible to send a link as a button and allow the users to open it in a Messenger webview:

JSON:
```json
{
  "type": "webview",
  "url": "https://asiapacific.unwomen.org/en/countries/india",
  "buttonText": "Visit UN Women",
  "extensions": false,
  "keepMoving": true
}
```

If you want to go further, Fly offers the ability to track link clicking. That can be done with the following snippet:

JSON:
```json
{
  "type": "webview",
  "url": {
    "base": "links.vlab.digital",
    "params": {
      "url": "asiapacific.unwomen.org/en/countries/india",
      "id": "{{hidden:id}}",
      "pageid": "YOUR_PAGE_ID"
    }
  },
  "buttonText": "Visit UN Women",
  "extensions": false,
  "keepMoving": true
}
```

To track specific information about the user, add metadata as query parameters in addition to `url` in the `url` value. For example, you can add the user id by adding `id` with a typeform ref to a "hidden field" called "id" (note, you will probably need to type out the "@id" part inside Typeform so it links it to a hidden field properly):

```json
{
  "type": "webview",
  "url": {
    "base": "links.vlab.digital",
    "params": {
      "url": "asiapacific.unwomen.org/en/countries/india",
      "id": "{{hidden:id}}",
      "pageid": "YOUR_PAGE_ID"
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
      "url": "asiapacific.unwomen.org/en/countries/india",
      "id": "{{hidden:id}}",
      "pageid": "YOUR_PAGE_ID"
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

You can set `extensions` to `true` if the page you are visiting is using Messenger Extensions (i.e. Fly Survey's "Moviehouse" which can be used to watch Vimeo videos and track video-watching events:

Moviehouse example, which waits until they either start watching the video OR one hour is passed:

```json
  {
    "type": "webview",
    "url": {
        "base": "virtuallab-videos.netlify.app",
        "params": {
            "id": "YOUR_VIMEO_ID",
            "pageId": "YOUR_FACEBOOK_PAGE_ID",
            "userId": "{{hidden:id}}"
        }
    },
    "buttonText": "Watch now!",
    "responseMessage": "Sorry, you need to watch the video before continuing",
    "extensions": true,
    "wait": {
      "op": "or",
      "vars": [
        {
          "type": "timeout",
          "value": "1 hour"
        },
        {
          "type": "external",
          "value": {
            "type": "moviehouse:play",
            "id": "YOUR_VIMEO_ID"
          }
        }
      ]
    }
  }

```

## Stitch

When stitching from one form to another, the "stitch" must be a statement:


JSON:
```json
{"type": "stitch",
 "stitch": { "form": "FORM_SHORTCODE" }}
```

Where `FORM_SHORTCODE` is the shortcode of the form you'd like to move to.

You can also include metadata:

JSON:
```json
{"type": "stitch",
 "stitch": { "form": "FORM_SHORTCODE", metadata: {"variable_name": "variable_answer" }}}
```

## Wait - Timeout

Timeouts allow you to pause your survey and continue it later, creating multi-wave surveys. See more under [Timeouts]({{< ref "fly/reference/timeouts.md">}}).


# Wait - External Events

You can wait on external events. For example, linksniffer events allow you to wait until someone clicks a link. Or MovieHouse events allow you to wait until someone has started (or finished) a video.

## Basic Wait Examples

For example, this JSON would wait for a moviehouse:play event with the video id 23456:

```json
{ "type": "wait",
 "wait": {
 "type": "external",
 "value": {
    "type": "moviehouse:play",
    "id": "23456"
  }}
}
```

If you want to wait for any moviehouse:play event, regardless of video id, you can simply omit part of the json from the "value" property:

```json
{ "type": "wait",
 "wait": {
 "type": "external",
 "value": {
    "type": "moviehouse:play"
  }}
}
```

## Complex Wait Logic with Operators

For more complex scenarios, you can use logical operators (`op`) and variables (`vars`) to create compound wait conditions. This allows you to wait for multiple events or create sophisticated logic.

### Available Operators

- **`and`**: All conditions must be fulfilled
- **`or`**: At least one condition must be fulfilled

### Basic AND Logic

Wait for both a link click AND a video play event:

```json
{
  "type": "wait",
  "wait": {
    "type": "external",
    "op": "and",
    "vars": [
      {
        "type": "external",
        "value": {
          "type": "linksniffer:click",
          "url": "https://example.com"
        }
      },
      {
        "type": "external",
        "value": {
          "type": "moviehouse:play",
          "id": "12345"
        }
      }
    ]
  }
}
```

### Basic OR Logic

Wait for either a link click OR a video play event:

```json
{
  "type": "wait",
  "wait": {
    "type": "external",
    "op": "or",
    "vars": [
      {
        "type": "external",
        "value": {
          "type": "linksniffer:click",
          "url": "https://example.com"
        }
      },
      {
        "type": "external",
        "value": {
          "type": "moviehouse:play",
          "id": "12345"
        }
      }
    ]
  }
}
```

### Nested Logic

You can create more complex nested conditions. For example, wait for (link click AND video start) OR (link click AND video finish):

```json
{
  "type": "wait",
  "wait": {
    "type": "external",
    "op": "or",
    "vars": [
      {
        "type": "external",
        "op": "and",
        "vars": [
          {
            "type": "external",
            "value": {
              "type": "linksniffer:click",
              "url": "https://example.com"
            }
          },
          {
            "type": "external",
            "value": {
              "type": "moviehouse:play",
              "id": "12345"
            }
          }
        ]
      },
      {
        "type": "external",
        "op": "and",
        "vars": [
          {
            "type": "external",
            "value": {
              "type": "linksniffer:click",
              "url": "https://example.com"
            }
          },
          {
            "type": "external",
            "value": {
              "type": "moviehouse:finish",
              "id": "12345"
            }
          }
        ]
      }
    ]
  }
}
```

### Mixed Event Types

You can combine different types of events in the same logical expression:

```json
{
  "type": "wait",
  "wait": {
    "type": "external",
    "op": "and",
    "vars": [
      {
        "type": "external",
        "value": {
          "type": "linksniffer:click",
          "url": "https://example.com"
        }
      },
      {
        "type": "timeout",
        "value": "5m"
      }
    ]
  }
}
```

This example waits for both a link click AND a 5-minute timeout to occur.

## Structure Reference

When using operators, the wait object should have this structure:

```json
{
  "type": "wait",
  "wait": {
    "type": "external",
    "op": "and|or",
    "vars": [
      // Array of wait conditions (can be simple or complex)
    ]
  }
}
```

Each item in the `vars` array can be:
- A simple wait condition (with `type` and `value`)
- Another complex condition (with `op` and `vars` for nesting)

This allows you to create arbitrarily complex logical expressions for your wait conditions.


## Payment

Read about payment question types under [Incentive Payments]({{< ref "fly/reference/incentive_payments.md">}})

## Passing Thread Control

To pass thread control to another app, tag your final question with the following:

```json
{
  "handoff": {
    "target_app_id": "123456789",
    "metadata": { "return_app_id": "987765432" }
  }
}
```

If you would like to pass control, then wait for it to come back before proceeding to the next question, you can combine this with a wait event:


```json
{
  "handoff": {
    "target_app_id": "123456789",
    "metadata": { "return_app_id": "987765432" }
  }
  "wait": {
    "type": "handover",
    "value": { "target_app_id": "123456789" }
  }
}
```
