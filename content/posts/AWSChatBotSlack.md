---
title: "AWS ChatBot and Slack for notifications from AWS CodePipeline"
date: 2023-01-04T13:38:51+02:00
tags: ["learning", "Work", "AWS", "Slack", "CodePipeline"]
authorTwitter: "pmmalinov01" 
author: "Pavel Malinov"
categories: ["AWS", "Slack", "CodePipeline"]
---


## The problem

I do believe I am not the only one who is bombarded with useless spam on my work email. I am getting emails about long forgotten systems that do backup of dead products,
Email about some change management requests that is nothing to do with my work or even in same department. I hate it because all of that spam is forcing my set up Email rules to delete or block or remove those emails.
But as they are build from multiple teams over a long stretch of time, those emails do not follow a template or any common structure. So I am writing rules either for each email type or few more general to capture the crap flying towards me.

## Context

This is why I have been pushing for more notifications in channel from tools like Slack or Teams or Chime. I know this will became the new spam place but it is a problem my children will have to solve. Hey, this is what the rest of use are doing with Global Warming and plastic in the ocians.
Currently, I am in AWS focus company that has an ever increasing footprint. We are using AFT(it will be a topic of a future post) to deploy our AWS Account.
AFT uses CodePipelines(and all other services with Code*). We are at more than 200 pipelines for the same number of AWS Accounts and we expect to grow even more.
I have set up basic notifications with `EventBridge>Transform>SNS>Email`. Those are great but the SPAM in the email with the lack of knowing if some will work the failed pipeline is driving me crazy. I need to do double work and either check the pipeline myself or ask the slack channel if someone is working it.
And here I wanted to add the notification we already have AWS ChatBot. 
Why Chatbot is seemed like the easiest and fastest way to something cheap with low code and less technical dept and not to set up webhooks and labmdas and so on...

The old notification system will say in place and to it I was hoping to modify it as :
```
EventBridge>Rule>Trigger|
                          -> Transform>SNS>Email
                          -> SNS>ChatBot>Slack Channel
```

## The Problem 
Amazing after couple of months fighting the internal IT department to install the god damn Slack APP of AWS and going to a security for audit of it. We got it and it was possible to set it up.
I used the AWS docs that seemed useful and proved to be a brain dead operation to connect a slack channel and AWS ChatBot.
It is possible to run aws command like from awscli in the chat and get results, mind that the results are formatted mess and can be formatted as it seems(to be proved either wise). But also AWS ChatBot does not support
some services. And after couple of hours of banging my head in that wall I found an Note in the docs that state:

*Event notifications from: CloudWatch Alarms, CodeBuild, CodeCommit, CodeDeploy, and CodePipeline are not currently supported via EventBridge rules.*

So after the time it took to set up the damn app I have achieved nothing and I am still stuck with the emails. Amazing sometimes I love Amazon for there genius and others I wish I never used them in the first place.

So for the future I will stick to AWS Lambda and webhooks to push and set up slack notification because I do not care about any corner cases I may hit if I used something too niche.
