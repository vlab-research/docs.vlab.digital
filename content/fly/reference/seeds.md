---
title: Random Seeds
weight: 7
---

## Using Random Seeds for Randomizing Logic

Seeds work via hidden fields. Create a hidden field named `seed_N`, where `N` is replaced with the number of arms you wish to randomize. For example: `seed_2`, `seed_3`, `seed_4`, `seed_5`,..., `seed_100`.

This hidden field will have the assignment of each user, which will be an integer between 1 and N. For example, if you made a hidden field called `seed_3`, each user will have a value of that field equal to 1, 2, or 3.

Now use the hidden field in your logic jumps. If, for example, you create a hidden field called `seed_3`, then create logic jumps such that:

if `seed_3 == 1` do A, if `seed_3 == 2` do B, if `seed_3 == 3` do C.

## Testing Random Seeds

Random seeds can be tested just like any other hidden field, using the following format (testing seed_2):

https://m.me/YOURPAGEID?ref=form.YOURSHORTCODE.seed_2.YOURSEED

In this case, you should replace YOURSEED with either `1` or `2`.
