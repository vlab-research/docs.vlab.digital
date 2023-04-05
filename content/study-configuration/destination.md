---
title: "Destination"
date: 2023-04-05T08:41:03+02:00
draft: false
---

Every study needs a destination, where do the recruitment ads send the users? Destinations need to be connected to Virtual Lab so that, not only does it know where to send people who click on the recruitment ads, but also so that it knows how to collect the information about those who become study participants and knows how to optimize ads.

Towards that end, Virtual Lab supports a set a "destinations" and is written in such a way that it is easy to add a new destination. When configuring your study, you can create one or more destinations and each destination needs a unique name (key) that you can refer to it by.

The following are currently supported along with their method of configuation:

Fly Messenger Survey Destination. This creates "Messenger ads" in Facebook for when you want the user to be directed to a Fly Messenger Survey. It requires the following parameters:

- `initial_shortcode`: str The shortcode of the initial form users should be sent to.
- `survey_name`: str The survey that contains all the forms with data of interest.

Typeform. Each Typeform destination needs the following parameters:

- `form_id`: str The Form ID of the Typeform you wish to send users to.

Curious Learning Literacy Apps. Requires the following parameters:

- `from`: int Starting timestamp for retrieving app events from Literacy Data API.
- `app_id`: str The app ID for the Google Play store.
- `facebook_app_id`: str he app ID for Facebook.
- `deeplink_template`: str
- `app_install_state`: str
- `user_device`: list[str]
- `user_os`: list[str]
- `attribution_id`: str
