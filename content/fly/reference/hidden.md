---
title: Hidden Fields
weight: 6
---

Sometimes we surface hidden fields that are too long to be added as hidden fields in Typeform. These can be accessed in the text of a question/statement using the following syntax:

```
{{hidden:e_payment_http_result_message_success}}
```

Where the "too long" hidden field is `e_payment_http_result_message_success` which would be populated, for example, if you had a http payment result that looked like this: `{"message": {"success": "foo"}}` and you wanted to show "foo".

## Interpolation

Fly supports mustache-style interpolation in question titles and descriptions. You can reference:

- **Hidden fields**: `{{hidden:field_name}}` — metadata from the user, referral params, or payment results
- **Previous answers**: `{{field:question_ref}}` — the raw answer to a previous question

### Interpolation Transforms

You can apply transforms to interpolated values using pipe syntax:

```
{{field:phone|e164}}
```

This looks up the answer to the `phone` question, then applies the `e164` transform to normalize it. Transforms are applied left-to-right, so chaining is possible:

```
{{field:phone|e164|lower}}
```

#### Available Transforms

| Transform | Description |
|-----------|-------------|
| `e164` | Normalizes a phone number to E.164 format (e.g. `+254712345678`). Strips trailing text and validates using the `phone` library. If the value cannot be normalized, the raw value is returned unchanged. |

#### Why `|e164` matters for payments

When a user answers a phone number question, their response may include trailing text — for example `+254712345678 use this` instead of just `+254712345678`. The raw answer is preserved as-is in the survey data, which is important for the audit trail.

However, payment providers require a clean E.164 phone number. Without the `|e164` transform, the messy raw value would be sent directly to the provider, which can cause:

1. **Payment failures** — the provider rejects the malformed number
2. **Duplicate payments** — two submissions of the same number with different trailing text produce different `custom_identifier` values, bypassing duplicate detection

To avoid these issues, always use `|e164` when referencing phone fields in payment details:

```json
"details": {
    "mobile": "{{field:phone|e164}}",
    "custom_identifier": "survey_x_{{field:phone|e164}}_1"
}
```

Without the transform, `{{field:phone}}` resolves to the raw stored value (e.g. `+254712345678 use this`). With `|e164`, it resolves to the normalized E.164 number (`+254712345678`).
