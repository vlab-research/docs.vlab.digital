---
title: "Destinations"
date: 2023-04-05T08:41:03+02:00
draft: false
weight: 4
---

Every study needs a destination, where do the recruitment ads send the users? Destinations need to be connected to Virtual Lab so that, not only does it know where to send people who click on the recruitment ads, but also so that it knows how to collect the information about those who become study participants and knows how to optimize ads.

Towards that end, Virtual Lab supports a set a "destinations" and is written in such a way that it is easy to add a new destination. When configuring your study, you can create one or more destinations and each destination needs a unique name (key) that you can refer to it by.

All destinations require the following fields:

- `name`: The name of the destination, used to refer to it in other configuration screens.

The following are currently supported along with their method of configuation:

## Messenger

This is a Fly Messenger Survey Destination. This creates "Messenger ads" in Facebook for when you want the user to be directed to a Fly Messenger Survey. It requires the following parameters:

- `Initial Shortcode`: The shortcode of the initial form users should be sent to.
- `Welcome Message`: When someone clicks on your ads, they are directed to a Messenger chat with this initial message.
- `Button Text`: This is the button that they click in the chat, in response to the welcome message, in order to start taking the survey.

## Web

- `Url Template`: The URL of the web survey, with `{ref}` wherever you want the Virtual Lab "ref" to be inserted into the URL. Virtual Lab "refs" contain a set of key-value pairs, separated by periods (`.`), containing metadata for each ad.

## App
Requires the following parameters:

- `Facebook App ID`: The app ID for Facebook.
- `Deeplink Template`: The deeplink template to link to the app, with `{ref}` wherever you want the Virtual Lab "ref" to be inserted into the URL. Virtual Lab "refs" contain a set of key-value pairs, separated by periods (`.`), containing metadata for each ad.
- `App Install State`: The app install state, as per Facebook
- `User Device`: The targeted devices.
- `User OS`: Targeted install states.
