---
title: Core Concepts
weight: 0
---

## Core concepts

Survey in Fly consist of one or more "Forms". "Surveys" themselves are nothing more then a collection of forms with a name. Surveys are the unit for downloading data - you download data for one "survey" all together.

Forms are parts of a survey. They consist of questions that a participant will answer. Forms are each given an individual "shortcode", which identifies the form.

Forms can "stitch" to other forms, stringing forms together longitudinally.

Also, forms can be connected to other forms as "translations", so that you can run your survey in multiple languages and translate everything back to a single language for analysis.

## Example 1: A multilingual longitudinal study

A multilingual longitudinal study might consist of four forms with the following shortcodes:

1. baselinespanish
2. baselinefrench
3. endlinespanish
4. endlinefrench

In this grouping, baselinespanish will probably be stitched with endlinespanish. If Spanish is the core languge of the researchers, they might want to translate all the French forms into Spanish so that the final survey data is downloaded entirely in Spanish.
