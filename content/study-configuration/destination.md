---
title: "Destination"
date: 2023-04-05T08:41:03+02:00
draft: false
---

Every study needs a destination, where do the recruitment ads send the users to? 

Destinations need to be connected to Virtual Lab so that, not only does it know where to send people who click on the recruitment ads, but also so that it knows how to collect the information about those who become study participants and knows how to optimize ads.

Towards that end, Virtual Lab supports a set of "destinations" and is written in such a way that it is easy to add a new destination. When configuring your study, you can create one or more destinations and each destination needs a unique name (key) that you can refer to it by.

There are currently three types of destinations available:

1. [Fly Messenger Survey](#fly-messenger-survey)
2. [Web Application](#web-application)
3. [Curious Learning Literacy App](#curious-learning-literacy-app)

## Fly Messenger Survey 

This creates "Messenger ads" in Facebook for when you want the user to be directed to a Fly Messenger Survey. 

We require the following to be set:

- `Initial Shortcode`: The shortcode of the initial form users should be sent to.
- `Survey Name`: The survey that contains all the forms with data of interest.

## Web Application

Web application destinations are links to send recruited users to, (i.e
a TypeForm survey)

Each Web Application Destination needs the following parameters:

- `URL Template`: The url where someone can start the survey (i.e typeform
    would be `https://example.typeform.com/to/ABCDEF?ref={ref}`)


## Curious Learning Literacy Apps

Requires the following parameters:

- `From`: Starting timestamp for retrieving app events from Literacy Data API.
- `App Id`: The app ID for the Google Play store.
- `Facebook App Id`: The app ID for Facebook.
- `deeplink_template`: str
- `App Install State`: 
- `User Device`:
- `User OS`: 
- `Attribution Id`:

