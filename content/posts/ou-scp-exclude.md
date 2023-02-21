---
title: "OU SCP Exclude"
date: 2023-02-21T14:31:34+02:00
draft: false
tags: ["learning", "aws", "Organization", "SCP"]
authorTwitter: "pmmalinov01" 
autho: "Pavel Malinov"
categories: [""]
---


# The problem
We are heavy users of AWS Organization and Service Control Policy. We are blocking a lot of stuff that is common foot guns and we do not need to deal with on daily basis.
One of our polices is to limit regions that are used across the organization. 
```
    {
      "Sid": "DenyAccessToBlockedRegions",
      "Effect": "Deny",
      "NotAction": [
        "cloudfront:*",
        "iam:*",
        "route53:*",
        "support:*",
        "directconnect:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": [
            "ca-central-1",
            "eu-central-1",
            "eu-west-1",
            "eu-west-2",
            "us-east-1",
            "us-east-2",
            "us-west-2"
          ]
        },
        "ArnNotLike": {
          "aws:PrincipalARN": "arn:aws:iam::*:role/AWSControlTowerExecution"
        }
      }
    }
```
> **_Note_** It is redacted for simplicity


The issue is once we have only 1 Organization Unit that needs a new region and the rest do not. For not going off topic it is due to political reasons.
To achieve that we have a few options:

1. Policy per OU to block all region, and custom policy for the OU needed the additional OU
2. One policy applied on the Root to block all region with condition to exclude the OU we need to add more regions. And a policy to that OU to allow the needed set of regions.
3. One SCP to the root that has a all region including the only one needed for only the special OU. And a second statement in the same policy deny that region for all OU except the particular OR.

We have picked to use the Option 3. To do so we are modifying the above policy:
```
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": [
            "ca-central-1",
            "eu-central-1",
            "eu-west-1",
            "eu-west-2",
            "us-east-1",
            "us-east-2",
            "us-west-2",
            "eu-west-3" < New Region
          ],
        }
      }
```

And adding a second statement like:

```
{
      "Sid": "fpp",
      "Effect": "Deny",
      "NotAction": [
        "cloudfront:*",
        "iam:*",
        "route53:*",
        "support:*",
        "directconnect:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:RequestedRegion": "eu-west-3"
        },
        "ForAnyValue:StringNotLike": {
          "aws:PrincipalOrgPaths": [
            "o-ourorgid/r-xxxx/ou-xxxx-yyy/ou-gggg-fffff/*"
          ]
        }
      }
    }
```

Now all OU still do not have access to eu-west-3 but the OU we picked to have access is allow as it is a reversed logic to the first policy
It is string deny for eu-west-3 but excluded for strings that match the Org Path of our OU.
