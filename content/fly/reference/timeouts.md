---
title: Timeouts
weight: 5
---



## Timeout Types

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


## Timeouts over 24 hours

Messenger only lets you send a normal message within 24 hours of the user's last activity. To send anything after that — survey results, prize notifications, reminders — you need a **Utility Message template**, pre-approved by Facebook for your Page.

> Earlier approaches (Message Tags like `CONFIRMED_EVENT_UPDATE`, and Recurring Notifications) were deprecated by Facebook in early 2026. Utility Messages are the current, globally-available replacement and require **no user opt-in**.

### 1. Create the template in the dashboard

In the dashboard, go to **Message Templates → Create Template**. Pick the page, name the template in `snake_case`, pick a language, and write the body. Use `{{1}}`, `{{2}}`, etc. for any values you will fill in at send time (e.g. the user's name).

**Add buttons** if you want users to tap instead of typing — this is almost always what you want, since free text after a long wait creates friction and is hard to branch on. Up to 3 buttons; labels are locked at approval and stay visible in the chat (POSTBACK buttons).

A template is identified by the tuple **(page, name, language)** — the same name can exist in multiple independently-approved language variants. If your survey runs in multiple languages, create the same template name once per language.

Facebook typically auto-approves custom utility templates in seconds. Wait until the row shows **Approved** before using it.

### 2. Use it in the survey after a long wait

Set up your wait step as described above (any timeout over 24 hours), then make the next field a `utility_message`. If the template has buttons, the field must be a **Multiple Choice** question whose choices match the approved button labels — buttons are NOT declared in the JSON, they come from the question's choices. If the template is text-only, use a **Statement** question.

```json
{
  "type": "utility_message",
  "template": "results_ready",
  "language": "en_US",
  "params": ["{{hidden:name}}", "$5"]
}
```

**Fields:**

| Field | Required | Notes |
|-------|----------|-------|
| `template` | Yes | The template name you created in the dashboard. |
| `language` | Yes | The locale you approved for this template variant (e.g. `en_US`, `es_LA`, `ha`). Must match exactly. No silent default — a missing language is an error. |
| `params` | Only if the body has placeholders | Positional array of values substituted into `{{1}}`, `{{2}}`, etc. in template order. Supports `{{hidden:X}}` interpolation. Length must equal the placeholder count. |

The `params` array corresponds 1-to-1 with the `{{N}}` placeholders in the body: the first element fills `{{1}}`, the second fills `{{2}}`, and so on. If your template body has 3 placeholders, pass 3 params.

For full setup details — including how the Multiple Choice question's choices map to the approved buttons and how to branch on a tap — see the [Utility Message question type]({{< ref "fly/reference/questions.md">}}#utility-message).
