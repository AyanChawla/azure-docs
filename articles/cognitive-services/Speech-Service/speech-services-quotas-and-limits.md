---
title: Speech service Quotas and Limits
titleSuffix: Azure Cognitive Services
description: Quick reference, detailed description, and best practices on Azure Cognitive Speech service Quotas and Limits
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/24/2022
ms.author: alexeyo
---

# Speech service Quotas and Limits

This article contains a quick reference and the **detailed description** of Azure Cognitive Speech service Quotas and Limits for all [pricing tiers](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). It also contains some best practices to avoid request throttling.

## Quotas and Limits quick reference
Jump to [Text-to-Speech Quotas and limits](#text-to-speech-quotas-and-limits-per-speech-resource)
### Speech-to-Text Quotas and Limits per Speech resource
In the following tables, the parameters without "Adjustable" row are **not** adjustable for all price tiers.

#### Online Transcription
For the usage with [Speech SDK](speech-sdk.md) and/or [Speech-to-text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio).

| Quota | Free (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **Concurrent Request limit - Base model endpoint** | 1 | 100 (default value) |
| Adjustable | No<sup>2</sup> | Yes<sup>2</sup> |
| **Concurrent Request limit - Custom endpoint** | 1 | 100 (default value) |
| Adjustable | No<sup>2</sup> | Yes<sup>2</sup> |

#### Batch Transcription
| Quota | Free (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| [Speech-to-text REST API V2.0 and v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) limit | Batch transcription is not available for F0 | 300 requests per minute |
| Max audio input file size | N/A | 1 GB |
| Max input blob size (may contain more than one file, for example, in a zip archive; ensure to note the file size limit above) | N/A | 2.5 GB |
| Max blob container size | N/A | 5 GB |
| Max number of blobs per container | N/A | 10000 |
| Max number of files per Transcription request (when using multiple content URLs as input) | N/A | 1000  |

#### Model Customization
| Quota | Free (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| REST API limit | 300 requests per minute | 300 requests per minute |
| Max number of speech datasets | 2 | 500 |
| Max acoustic dataset file size for Data Import | 2 GB | 2 GB |
| Max language dataset file size for Data Import | 200 MB | 1.5 GB |
| Max pronunciation dataset file size for Data Import | 1 KB | 1 MB |
| Max text size when using `text` parameter in [Create Model](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) API request | 200 KB | 500 KB |

<sup>1</sup> For **Free (F0)** pricing tier see also monthly allowances at the [pricing page](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>2</sup> See [additional explanations](#detailed-description-quota-adjustment-and-best-practices), [best practices](#general-best-practices-to-mitigate-throttling-during-autoscaling),  and [adjustment instructions](#speech-to-text-increasing-online-transcription-concurrent-request-limit).<br/>

### Text-to-Speech Quotas and limits per Speech resource
In the tables below Parameters without "Adjustable" row are **not** adjustable for all price tiers.

#### General

| Quota | Free (F0)<sup>3</sup> | Standard (S0) |
|--|--|--|
| **Max number of Transactions per Second (TPS) per Speech resource** |  |  |
| Real-time API. Prebuilt neural voices and custom neural voices | 200<sup>4</sup> | 200<sup>4</sup> |
| Adjustable | No<sup>4</sup> | Yes<sup>4</sup> |
| **HTTP-specific quotas** |  |  |
| Max Audio length produced per request | 10 min | 10 min |
| Max total number of distinct `<voice>` and `<audio>` tags in SSML | 50 | 50 |
| **Websocket specific quotas** |  |  |
| Max Audio length produced per turn | 10 min | 10 min |
| Max total number of distinct `<voice>` and `<audio>` tags in SSML | 50 | 50 |
| Max SSML Message size per turn | 64 KB | 64 KB |

#### Long Audio API

| Quota | Free (F0)<sup>3</sup> | Standard (S0) |
|--|--|--|
| Min text length | N/A | 400 characters for plain text; 400 [billable characters](text-to-speech.md#pricing-note) for SSML |
| Max text length | N/A | 10000 paragraphs |
| Start time | N/A | 10 tasks or 10000 characters accumulated |

#### Custom neural voice

| Quota | Free (F0)<sup>3</sup> | Standard (S0) |
|--|--|--|
| Max number of Transactions per Second (TPS) per Speech resource | [See above](#general) | [See above](#general) |
| Max number of data sets per Speech resource | 10 | 500 |
| Max number of simultaneous dataset upload per Speech resource | 2 | 5 |
| Max data file size for data import per dataset | 2 GB | 2 GB |
| Upload of long audios or audios without script | No | Yes |
| Max number of simultaneous model trainings per Speech resource | N/A | 3 |
| Max number of custom endpoints per Speech resource | N/A | 50 |
| **Concurrent Request limit for custom neural voice** |  |  |
| Default value | N/A | 10 |
| Adjustable | N/A | Yes<sup>5</sup> |

<sup>3</sup> For **Free (F0)** pricing tier see also monthly allowances at the [pricing page](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>4</sup> See [additional explanations](#detailed-description-quota-adjustment-and-best-practices) and [best practices](#general-best-practices-to-mitigate-throttling-during-autoscaling).<br/>
<sup>5</sup> See [additional explanations](#detailed-description-quota-adjustment-and-best-practices), [best practices](#general-best-practices-to-mitigate-throttling-during-autoscaling),  and [adjustment instructions](#text-to-speech-increasing-concurrent-request-limit-for-custom-neural-voices).<br/>

## Detailed description, Quota adjustment, and best practices
Before requesting a quota increase (where applicable), ensure that it is necessary. Speech service is using autoscaling technologies to bring the required computational resources in "on-demand" mode and at the same time to keep the customer costs low by not maintaining an excessive amount of hardware capacity. Every time your application receives a Response Code 429 ("Too many requests") while your workload is within the defined limits (see [Quotas and Limits quick reference](#quotas-and-limits-quick-reference)) the most likely explanation is that the Service is scaling up to your demand and did not reach the required scale yet, thus does not immediately have enough resources to serve the request. This state is usually transient and should not last long.

### General best practices to mitigate throttling during autoscaling
To minimize issues related to throttling (Response Code 429), we recommend using the following techniques:
- Implement retry logic in your application
- Avoid sharp changes in the workload. Increase the workload gradually <br/>
*Example.* Your application is using text-to-speech and your current workload is 5 Transactions per Second (TPS). The next second you increase the load to 20 TPS (that is four times more). The Service immediately starts scaling up to fulfill the new load, but likely it will not be able to do it within a second, so some of the requests will get Response Code 429.
- Test different load increase patterns. See the [workload pattern example](#example-of-a-workload-pattern-best-practice)
- Create additional Speech resources in the same or different Regions and distribute the workload among them using "Round Robin" technique. This is especially important for the text-to-speech Transactions per Second (TPS) parameter, which is set to 200 per Speech resource and cannot be adjusted.

The next sections describe specific cases of adjusting quotas.<br/>
Jump to [Text-to-Speech: increasing concurrent request limit for custom neural voices](#text-to-speech-increasing-concurrent-request-limit-for-custom-neural-voices)

### Speech-to-text: increasing online transcription concurrent request limit
By default the number of concurrent requests is limited to 100 per Speech resource (Base model) and to 100 per Custom endpoint (Custom model). For the Standard pricing tier, this amount can be increased. Before submitting the request, ensure you are familiar with the material in [this section](#detailed-description-quota-adjustment-and-best-practices) and aware of these [best practices](#general-best-practices-to-mitigate-throttling-during-autoscaling).

>[!NOTE]
> If you use custom models, please be aware, that one Speech resource may be associated with many custom endpoints hosting many custom model deployments. Each Custom endpoint has the default number of concurrent request limit (100) set by creation. If you need to adjust it, you need to make the adjustment of each custom endpoint **separately**. Please also note, that the value of the number of concurrent request limit for the base model of a Speech resource has **no** effect to the custom endpoints associated with this resource.

Increasing the Concurrent Request limit does **not** directly affect your costs. Speech service uses "Pay only for what you use" model. The limit defines how high the Service may scale before it starts throttle your requests.

Concurrent Request limits for **Base** and **Custom** models need to be adjusted **separately**.

Existing value of Concurrent Request limit parameter is **not** visible via Azure portal, Command-Line tools, or API requests. To verify the existing value, create an Azure Support Request.

>[!NOTE]
>[Speech containers](speech-container-howto.md) do not require increases of Concurrent Request limit, as containers are constrained only by the CPUs of the hardware they are hosted on. However Speech containers have their own capacity limitations that should be taken into account. See the question *"Could you help with capacity planning and cost estimation of on-prem Speech-to-text containers?"* from the [Speech containers FAQ](./speech-container-howto.md).

#### Have the required information ready:
- For **Base model**:
  - Speech Resource ID
  - Region
- For **Custom model**:
  - Region
  - Custom Endpoint ID

- **How to get information (Base model)**:
  - Go to [Azure portal](https://portal.azure.com/)
  - Select the Speech Resource for which you would like to increase the Concurrency Request limit
  - Select *Properties* (*Resource Management* group)
  - Copy and save the values of the following fields:
    - **Resource ID**
    - **Location** (your endpoint Region)

- **How to get information (Custom Model)**:
  - Go to [Speech Studio](https://speech.microsoft.com/) portal
  - Sign in if necessary
  - Go to Custom Speech
  - Select your project
  - Go to *Deployment*
  - Select the required Endpoint
  - Copy and save the values of the following fields:
    - **Service Region** (your endpoint Region)
    - **Endpoint ID**

#### Create and submit support request
Initiate the increase of Concurrent Request limit for your resource or if necessary check the today's limit by submitting the Support Request:

- Ensure you have the [required information](#have-the-required-information-ready)
- Go to [Azure portal](https://portal.azure.com/)
- Select the Speech Resource for which you would like to increase (or to check) the Concurrency Request limit
- Select *New support request* (*Support + troubleshooting* group)
- A new window will appear with auto-populated information about your Azure Subscription and Azure Resource
- Enter *Summary* (like "Increase STT Concurrency Request limit")
- In *Problem type* select "Quota or Subscription issues"
- In appeared *Problem subtype* select:
  - "Quota or concurrent requests increase" - for an increase request
  - "Quota or usage validation" to check existing limit
- Click *Next: Solutions*
- Proceed further with the request creation
- When in *Details* tab enter in the *Description* field:
  - a note, that the request is about **Speech-to-Text** quota
  - **Base** or **Custom** model
  - Azure resource information you [collected before](#have-the-required-information-ready)
  - Complete entering the required information and click *Create* button in *Review + create* tab
  - Note the support request number in Azure portal notifications. You will be contacted shortly for further processing

### Example of a workload pattern best practice
This example presents the approach we recommend following to mitigate possible request throttling due to [Autoscaling being in progress](#detailed-description-quota-adjustment-and-best-practices). It is not an "exact recipe", but merely a template we invite to follow and adjust as necessary.

Let us suppose that a Speech resource has the Concurrent Request limit set to 300. Start the workload from 20 concurrent connections and increase the load by 20 concurrent connections every 1.5-2 minutes. Control the Service responses and implement the logic that falls back (reduces the load) if you get too many Response Codes 429. Then retry in 1-2-4-4 minute pattern. (That is retry the load increase in 1 min, if still does not work, then in 2 min, and so on)

Generally, it is highly recommended to test the workload and the workload patterns before going to production.

### Text-to-Speech: increasing concurrent request limit for custom neural voices

By default the number of concurrent requests for custom neural voice endpoints is limited to 10. For the Standard pricing tier, this amount can be increased. Before submitting the request, ensure you are familiar with the material in [this section](#detailed-description-quota-adjustment-and-best-practices) and aware of these [best practices](#general-best-practices-to-mitigate-throttling-during-autoscaling).

Increasing the Concurrent Request limit does **not** directly affect your costs. Speech service uses "Pay only for what you use" model. The limit defines how high the Service may scale before it starts throttle your requests.

Existing value of Concurrent Request limit parameter is **not** visible via Azure portal, Command-Line tools, or API requests. To verify the existing value, create an Azure Support Request.

>[!NOTE]
>[Speech containers](speech-container-howto.md) do not require increases of Concurrent Request limit, as containers are constrained only by the CPUs of the hardware they are hosted on.

#### Prepare the required information:
To create an increase request, you will need to provide your Deployment Region and the Custom Endpoint ID. To get it, perform the following actions:

- Go to [Speech Studio](https://speech.microsoft.com/) portal
- Sign in if necessary
- Go to *Custom Voice*
- Select your project
- Go to *Deployment*
- Select the required Endpoint
- Copy and save the values of the following fields:
    - **Service Region** (your endpoint Region)
    - **Endpoint ID**

#### Create and submit support request
Initiate the increase of Concurrent Request limit for your resource or if necessary check the today's limit by submitting the Support Request:

- Ensure you have the [required information](#prepare-the-required-information)
- Go to [Azure portal](https://portal.azure.com/)
- Select the Speech Resource for which you would like to increase (or to check) the Concurrency Request limit
- Select *New support request* (*Support + troubleshooting* group)
- A new window will appear with auto-populated information about your Azure Subscription and Azure Resource
- Enter *Summary* (like "Increase TTS Custom Endpoint Concurrency Request limit")
- In *Problem type* select "Quota or Subscription issues"
- In appeared *Problem subtype* select:
  - "Quota or concurrent requests increase" - for an increase request
  - "Quota or usage validation" to check existing limit
- Click *Next: Solutions*
- Proceed further with the request creation
- When in *Details* tab enter in the *Description* field:
  - a note, that the request is about **Text-to-Speech** quota
  - Azure resource information you [collected before](#prepare-the-required-information)
  - Complete entering the required information and click *Create* button in *Review + create* tab
  - Note the support request number in Azure portal notifications. You will be contacted shortly for further processing
