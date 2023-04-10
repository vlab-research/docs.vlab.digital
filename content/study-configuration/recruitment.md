---
title: "Recruitment"
date: 2023-04-05T08:40:52+02:00
draft: false
---

The "recruitment" configuration describes how/where recruitment will take place. Every study needs to recruit from somewhere.

There are currently 3 types of recruitments:

1. [Simple](#simple)
2. [Pipeline Experiment](#pipeline-experiment)
3. [Destination Experiment](#destination-experiment)

## Simple

This is the easiest of the recruiting strategies where the Virtual Labs platform will
routinely check your ad campaign and update the campaign accordingly to try and
achieve your recruiting goals.

![recruitment simple conf](/images/recruitment-simple-conf.png)

You will need to provide the following fields:

- `Ad Campaign Name`: This is the name of the ad campaign that this recruitment
    should be targeting
- `Budget`: The amount you would like to spend in order to recruit your target
- `Max Sample`: This is the maximum sample size
- `Start Date`: The date in which the recruiting should start
- `End Date`: The date on which the recruiting should end

## Destination Experiment 

Use this when you want to create a multi-arm randomized experiment (A/B test on Facebook) where some of your sample is sent to different [destinations]({{< ref destination.md >}} "Destination Configuration"). 


You will need to have the destinations configured and you will then need to set the following fields:

- `Ad Campaign Name Base`: The name of the ad campaign(s) that vlab will create and use for recruitment. If you have more than one campaign (i.e. Destination Experiment), it will append -1, -2, etc. to this name
- `Budget Per Arm`: How much you would like to spend per arm of your experiment
- `Max Sample Per Arm`: How big of a sample you would like to set for each arm
    of your experiment
- `Destinations`: The list of destinations that should be involved within the
    experiment
- `Start Date`: The date in which the recruiting should start
- `End Date`: The date on which the recruiting should end


## Pipeline Experiment

In this design, we generate an A/B test on Facebook but instead of sending your sample to different destinations, we run the ads at different times (a pipeline experiment design). You can set up how long each arm runs, and how long they are offset from the start of the previous arm.

![recruitment pipeline conf](/images/recruitment-pipeline-conf.png)

You will need to provide the following fields:

- `Ad Campaign Name Base`: The name of the ad campaign(s) that vlab will create and use for recruitment. If you have more than one campaign (i.e. Destination Experiment), it will append -1, -2, etc. to this name
- `Budget Per Arm`: How much you would like to spend per arm of your experiment
- `Max Sample Per Arm`: How big of a sample you would like to set for each arm
- `Start Date`: The date in which the recruiting should start
- `End Date`: The date on which the recruiting should end
- `Arms`: How many arms of the experiment would you like to create
- `Recruitment Days`: How many days you would like the recruitment to run for
- `Offset Days`: How many days you would like to pause the recruiting for
