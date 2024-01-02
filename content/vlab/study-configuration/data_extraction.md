---
title: Data Extraction
weight: 18
---

In order to know which respondent corresponds to which strata, and who "finished," we need to extract "variables" from the data source.

Virtual Lab maps variables from data sources into an internal name and format, so that it can optimize over them. This is where you define that mapping.

The way you define the mapping depends on the data source, but there are some shared fields:

- `Name`: This is the name of the variable as you wish it to be referred to in Virtual Lab. For example, this should be the name of a variable defined under Variables or under Strata (Finished Question Ref).

The rest depend on the data source:

# Fly

- `Location`: Where is the data located, is it in "metadata" or is it an individual "variable" (question)?
- `Key`: The name of the variable, within Fly, that you want to extract. This is the "question ref" defined when creating the Fly survey.
- `Response`: Whether or not you want the value of the raw response or the "translated response" from Fly.


# Qualtrics

- `Location`: Where is the data located, is it in "metadata" (user metadata) or is it an individual "variable" (question)?
- `Key`: The name of the variable, within Qualtrics, that you want to extract. This often has the form of "Q11" or something to that effect.
