---
title: Data Extraction
weight: 18
---

In order to know which respondent corresponds to which strata, and who "finished," we need to extract "variables" from the data source.

Virtual Lab maps variables from data sources into an internal name and format, so that it can optimize over them. This is where you define that mapping. *All variables must be defined in order for the optimization to work*.

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

# Common Patterns

## Pattern #1: Trusting Meta for Strata

This is the simplest and most common pattern.

As an example, let's pretend that you have created (3) different variables under Variables:

1. Gender
2. Age
3. Location

These variables might have multiple levels, or some of them might only have one level. Regardless, we need to tell vlab how to assign individuals to strata for the optimization to work.

The simplest form of assignment is to use the ad set metadata itself to define assignment. If they come through the ad set that targets Gender:Women,Age:65+,Location:All, then we simply assume that the person belongs in that strata.

To follow this pattern, simply create data extractions that look like this for Gender:

- `Name: Gender`
- `Location: Metadata`
- `Key: Gender`

And repeat the same for Age and Location.

Finally, we need the finished question ref. Usually, this is in the variable, not in the metadata. Put together, the config looks like this:

![](/images/data-extraction-example-1.png)


## Pattern #2: Validating Strata in Survey

Often, we want to directly ask people in our survey about their demographics, such as age and gender, even though we are already using Facebook to target based on that information. While we generally expect Facebook's demographics and self-reported demographics to be highly correlated, if the exact proportions are very important, it might be useful to extract that information from the survey.

In that case, you can replace the Metadata extractions with Variable extractions. You could do this for all of them or part of them. In this example, we override age and gender with data collected in the survey. However, we stick with Metadata for location:

![](/images/data-extraction-example-2.png)
