---
layout: ../../layouts/Article.astro
kind: project
title: A fairness testing platform for AI models in life insurance
description: How I built RGA's discrimination testing platform for predictive and AI models as its sole engineer — from regulation and statistics to production in ten weeks.
org: Reinsurance Group of America
period: June–August 2026
role: Sole engineer
stack: [Python, Litestar, Pydantic, React, TypeScript, visx, OpenAPI, PySpark, Databricks]
meta: [Reinsurance Group of America, Summer 2026, Sole engineer, 10 weeks]
---

## The problem

New regulation — the EU AI Act, plus life-insurance-specific rules in the US, Canada, China and elsewhere — requires that predictive and AI models be tested for discrimination before they're used. RGA had the obligation and no tooling to meet it. There was no process, no shared definition of what a fair model meant in this context, and no way for a model owner to check their own work. A single calibration test, done by hand by the model risk team, took multiple weeks.

I spent ten weeks as the only engineer on the fix.

## Three constraints that shaped everything

*The data couldn't move.* Privacy rules meant training data and models could not leave their AWS region or Databricks workspace — which is exactly why the model risk team and the EMEA data science org had been unable to test anything together. Rather than build a service that ingests data, I built one that goes to the data: a Databricks job that runs on behalf of the user, under their own credentials, against their own model. That design is what unblocked adoption in Europe.

*Most published fairness methods assume a binary decision.* The four-fifths rule, the disparate impact literature, most of what the external consultants proposed — all of it assumes the model outputs approve or deny. RGA's models mostly don't: they output mortality risk scores and premiums, which are continuous. I flagged the gap during the guideline drafting, proposed ANOVA with an omega-squared effect size as the replacement test for continuous outputs, and argued it through with the consulting principal. It was adopted into the corporate policy.

*The users aren't fairness researchers.* Model owners and actuaries needed to understand which tests would run against their model and why, before running anything.

## What I built

A full-stack application. A Litestar and Pydantic backend with roughly twenty REST endpoints, a React and TypeScript frontend using visx for the charts, and OpenAPI with openapi-typescript keeping the two type systems in sync. A model owner uploads a dataset and a model, configures the run, and gets back a formatted dashboard.

Underneath it, a suite of about a dozen statistical tests organized around three definitions of fairness: *inequality* — are there differences between protected groups at all; *residual inequality* — is the model systematically over- or under-predicting for some groups, which is a calibration question; and *unequal actionability* — do the marginal effects of the variables a person can actually change differ across groups. A proxy-detection step using adjusted mutual information and Cramér's V flags variables that stand in for protected attributes. Inference of protected attributes is left deliberately to the user — the tool does not guess someone's race.

The statistics run in PySpark, so a few hundred million rows finish in minutes rather than hours. Poisson GLMs are supported natively, since a lot of RGA's models are Poisson GLMs. Every run writes to a Databricks Delta audit table for the model risk team.

## Making it something people would use

I ran more than twenty discovery interviews across seven business units in the Americas and EMEA, and demoed to model owners and actuaries mid-build. That feedback produced the single biggest change: the configuration step was rebuilt as a wizard that shows, live, which tests each choice will trigger. At the end I gave an hour-long training to roughly seventy data scientists, model owners, actuaries, and three regional VPs of AI — which meant teaching a new tool, a new process, and the difference between statistical and practical significance, in that order.

## Outcome

Deployed to production as a Databricks App in ten weeks — about 10,000 lines across a monorepo, with CI, type checking, unit tests, synthetic datasets carrying known injected bias for validation, and a reviewed design and methodology document. It's now in beta rollout across a 70-person global data science organization. The team's write-up to leadership estimated it saves on the order of 2,500 analyst hours a year.

## What I'd do differently

I built the configuration screen twice. The first version was statistically correct and practically unusable: a user could set it up without any sense of which tests their choices would trigger, or why. I found that out in a week-six demo and rebuilt it as a wizard. A paper prototype in front of three model owners in week two would have cost an afternoon and saved a week.

The general lesson stuck with me. For a compliance tool, the statistics are the part you can look up. The hard part is getting someone to trust the output enough to act on it.
