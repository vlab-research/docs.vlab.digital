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

## Button Choice

Button Choice is similar to Multiple Choice, but displays options as persistent buttons instead of quick replies. Quick replies disappear after the user taps one, while button template buttons remain visible in the chat.

In Typeform, pick "Multiple Choice", then add the following to the description:

```json
{"type": "button_choice"}
```

**Limitations:**

1. Maximum of **3 buttons** (Facebook API limit). Use Multiple Choice for more options.
2. Button titles are truncated at 20 characters by Facebook.
3. Template text is limited to 640 characters.

**When to use Button Choice:**

- For questions with 3 or fewer options where you want buttons to persist visually
- Yes/No/Maybe style questions with a button appearance
- When you want the visual distinction of button templates over quick replies

**Example Setup:**

Create a Multiple Choice question in Typeform with your options (up to 3), then add to the description:

```json
{"type": "button_choice"}
```

The bot will display a button template with postback buttons instead of quick replies.

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

### Using Special URI Schemes (tel, mailto, sms)

In addition to HTTP and HTTPS links, linksniffer supports special URI schemes like `tel:`, `mailto:`, `sms:`, and others. This allows you to create buttons that trigger phone calls, compose emails, or send SMS messages directly from your chatbot.

The linksniffer service accepts an optional `p` (protocol) parameter to specify which URI scheme to use:

- **`https`** (default) - Standard web links
- **`http`** - Non-secure web links
- **`tel`** - Phone number links (triggers phone dialer)
- **`mailto`** - Email links (opens email client)
- **`sms`** - SMS links (opens messaging app)
- **Other single-colon schemes** - Any valid URI scheme (e.g., `whatsapp:`)

**Protocol formatting:**
- HTTP and HTTPS use `://` separator: `https://example.com`
- All other schemes use `:` separator: `tel:+1234567890`, `mailto:user@example.com`

#### Example: Phone Number Link

To create a button that opens the phone dialer:

```json
{
  "type": "webview",
  "url": {
    "base": "links.vlab.digital",
    "params": {
      "url": "+1-555-123-4567",
      "p": "tel",
      "id": "{{hidden:id}}",
      "pageid": "YOUR_PAGE_ID"
    }
  },
  "buttonText": "Call Support",
  "extensions": false,
  "keepMoving": true
}
```

This will generate a link like `tel:+1-555-123-4567` that opens the device's phone dialer when clicked. The click is still tracked with the user's `id` and `pageid` for analytics.

#### Example: Email Link

To create a button that opens the email client:

```json
{
  "type": "webview",
  "url": {
    "base": "links.vlab.digital",
    "params": {
      "url": "support@example.com",
      "p": "mailto",
      "id": "{{hidden:id}}",
      "pageid": "YOUR_PAGE_ID"
    }
  },
  "buttonText": "Email Us",
  "extensions": false,
  "keepMoving": true
}
```

This generates `mailto:support@example.com` and tracks when users click the email button.

#### Example: SMS Link

To create a button that opens the messaging app:

```json
{
  "type": "webview",
  "url": {
    "base": "links.vlab.digital",
    "params": {
      "url": "+1-555-123-4567",
      "p": "sms",
      "id": "{{hidden:id}}",
      "pageid": "YOUR_PAGE_ID"
    }
  },
  "buttonText": "Text Us",
  "extensions": false,
  "keepMoving": true
}
```

#### Waiting for Special Link Clicks

You can use wait conditions with special URI schemes just like regular links:

```json
{
  "type": "webview",
  "url": {
    "base": "links.vlab.digital",
    "params": {
      "url": "+1-555-123-4567",
      "p": "tel",
      "id": "{{hidden:id}}",
      "pageid": "YOUR_PAGE_ID"
    }
  },
  "buttonText": "Call Support",
  "responseMessage": "Please call us to continue",
  "extensions": false,
  "wait": {
    "type": "external",
    "value": {
      "type": "linksniffer:click"
    }
  }
}
```

**Note:** All link clicks through linksniffer are tracked and generate events, regardless of the protocol used. This allows you to track when users initiate phone calls, compose emails, or perform other actions triggered by special URI schemes.

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


## Utility Message

Sends a pre-approved Facebook **Utility Message template** to the user. Use this for notifications that need to reach the user after the 24-hour Messenger window has closed — survey results, prize notifications, reminders.

> Utility Messages are the current replacement for the deprecated Message Tags (`CONFIRMED_EVENT_UPDATE`, etc.) and Recurring Notifications. They require no user opt-in but do require a template that Facebook has approved for your Page.

### Setting it up

First, create the template in the dashboard under **Message Templates**. A template is identified by the tuple **(page, name, language)** — the same template name can exist in multiple independently-approved language variants. Use `{{1}}`, `{{2}}`, etc. in the body for positional placeholders.

Then, in Typeform create a **Statement** question and put this in the description:

```json
{
  "type": "utility_message",
  "keepMoving": true,
  "template": "results_ready",
  "language": "en_US",
  "params": ["{{hidden:name}}", "$5"]
}
```

**Fields:**

| Field | Required | Notes |
|-------|----------|-------|
| `template` | Yes | The template name you created in the dashboard. |
| `language` | Yes | The exact locale of the approved variant (e.g. `en_US`, `es_LA`, `ha`). No silent default — missing `language` is an error. |
| `params` | No | Positional array of values substituted into `{{1}}`, `{{2}}`, etc. Supports `{{hidden:X}}` interpolation. |

The `params` array is positional: the first element fills `{{1}}`, the second fills `{{2}}`, and so on. Mismatched counts cause a send-time error, so pass exactly as many params as the approved template has placeholders.

### Typical survey flow

Utility messages are usually sent after a long-running Wait — that's when the 24-hour window matters:

```
[Consent questions]
       ↓
[Wait - timeout: 3 days]
       ↓
[Utility Message] "Your {{1}} results are in, {{2}}!"   ← sent outside the 24h window
```

See [Timeouts]({{< ref "fly/reference/timeouts.md">}}) for timeout setup.

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
