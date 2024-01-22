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

You must ask permission from users to follow up with them after 24 hours on Messenger.

If you have done that, you can put the following in the next message and make the timeout > 24 hours:

After a timeout, you need to tag the next message as follows:

JSON:
```json
{
    "sendParams": {
        "tag": "CONFIRMED_EVENT_UPDATE",
        "messaging_type": "MESSAGE_TAG"
    }
}
```
