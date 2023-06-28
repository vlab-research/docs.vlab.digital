---
title: Hidden Fields
weight: 6
---

Sometimes we surface hidden fields that are too long to be added as hidden fields in Typeform. These can be accessed in the text of a question/statement using the following syntax:

```
{{hidden:e_payment_http_result_message_success}}
```

Where the "too long" hidden field is `e_payment_http_result_message_success` which would be populated, for example, if you had a http payment result that looked like this: `{"message": {"success": "foo"}}` and you wanted to show "foo".
