---
layout: post
title: "Application Composer - High-Level Architecture Visualization"
date: 2023-01-30 19:00:00 +0300
category: architecture
tags: 
  - aws
  - architecture
---
Amazon announced many new services in their AWS suite on the latest re:Invent 2022. One of them is the Application Composer service that Amazon CTO Dr. Werner Vogels presented as a part of his keynote. How often have you needed to build your high-level understanding of a complex project infrastructure having just a couple of outdated diagrams and detailed Terraform, SAM, or CloudFront templates? Even if you haven’t done it for some reason so far, taking into account the amount of existing software running all around, you will inevitably encounter it sooner than later. And it is the situation where a tool like Application Composer can give you a helping hand.<!--more-->

# What is Exactly the Application Composer Service?
Citing [AWS documentation](https://docs.aws.amazon.com/application-composer/latest/dg/what-is-composer.html){:target="_blank"}, "AWS Application Composer is a visual designer that you can use to build your serverless application from multiple AWS services" (AWS). Application Composer provides a canvas which you use to visually build your architecture from **serverless** components. You do this by dragging widgets of AWS serverless services from the resource pane, dropping them on the canvas and connecting them according to communication paths of your architecture. Application Composer is developing code template in YAML format while you are creating your architecture in the designer.
Below is an example of an architecture for a web application hosting its frontend in CloudFront and backend in AWS Lambda through API Gateway with data stored in PostgreSQL:

[CREATE ARCHITECTURE AND PUT PICTURE HERE]

Application Composer is currently in preview. You can build your architecture using the following [types](https://docs.aws.amazon.com/application-composer/latest/dg/reference-resources.html){:target="_blank"} of available resources:
| **Resource Type** | **Description** |
| ----------------- | --------------- |
| Featured resource | They are available from the resource palette. You can drag and drop them to the canvas and fully configure their properties. The following resources are supported so far: <br /> - API Gateway <br /> - Cognito UserPool and UserPoolClient <br /> - DynamoDB Table <br /> - EventBridge Event rule and EventBridge Schedule <br /> - Kinesis Stream <br /> - Lambda Function and Lambda Layer <br /> - S3 Bucket <br /> - SNS Topic <br /> - SQS Queue <br /> - Step Functions State machine |
| Hidden resource   | They are not available from the resource palette. You can add them by editing your YAML template. However, according to the [documentation](https://docs.aws.amazon.com/application-composer/latest/dg/reference-resources.html#reference-resources-hidden){:target="_blank"}, "their complete feature set of properties and integrations with other resoursses may not be supported by Application Composer". |
| Readonly resource | They are not available from the resource palette. You can add them by editing your YAML template. Application Composer visualizes readonly resources on the canvas but does not allow to configure them. |
| | |

# How does Application Composer Work with Template Files?
*******Application Composer can work with your file system in connected or unconnected mode. In connected mode, it automatically synchronizes all the changes to the template file on your drive (works in Chrome and Microsoft Edge and requires your confirmation to access local file system). In unconnected mode, you need to save all changes to the template file on your drive manually (HOW???).

If your architecture goes beyond the set of featured resources, AWS documentaiton [recommends](https://docs.aws.amazon.com/application-composer/latest/dg/other-services-ide.html){:target="_blank"} to use your IDE to change readonly or hidden resources directly in the YAML template file. The architecture will be automatically synced to Application Composer if you use Application Composer in the connected mode (see below).

You can use the template file developed by Application Composer to deploy the architecture to AWS by feeding it to [SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html){:target="_blank"}. The template code is available in online editor:

[PICTURE OF THE ONLINE EDITOR HERE]

You can deploy your template file to AWS using any AWS CloudFront compatible service. AWS Documentation [recommends](https://docs.aws.amazon.com/application-composer/latest/dg/other-services-cfn.html){:target="_blank"} the AWS SAM for this.

# What about Security and Compliance?

# Conclusions

# References
AWS. "What is AWS Application Composer? - AWS Application Composer." AWS Documentation, 01 12 2022, https://docs.aws.amazon.com/application-composer/latest/dg/what-is-composer.html. Accessed 30 January 2023.