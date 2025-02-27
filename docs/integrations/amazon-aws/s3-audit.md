---
id: s3-audit
title: Sumo Logic App for Amazon S3 Audit
sidebar_label: Amazon S3 Audit
description: Provides a simple web services interface that can be used to store and retrieve any amount of data from anywhere on the web.
---

import useBaseUrl from '@docusaurus/useBaseUrl';

<img src={useBaseUrl('img/integrations/amazon-aws/s3audit.png')} alt="Thumbnail icon" width="50"/>

Amazon Simple Storage Service (S3) provides a simple web services interface that can be used to store and retrieve any amount of data from anywhere on the web. The Sumo Logic App for Amazon S3 Audit presents details from access logs that contain information about the request type, the average response time, and the inbound and outbound data volume.

Our new app install flow is now in Beta. It is only enabled for certain customers while we gather Beta customer feedback. If you can see the Add Integration button, follow the "Before you begin" section in the "Collect Logs" help page and then use the in-product instructions in Sumo Logic to set up the app.


## Log Types

Amazon S3 Audit uses Server Access Logs (activity logs). For more information on the log format, see: [http://docs.aws.amazon.com/AmazonS3/latest/dev/LogFormat.html](http://docs.aws.amazon.com/AmazonS3/latest/dev/LogFormat.html).


### Sample Log Message

The server access log files consist of a sequence of new-line delimited log records. Each log record represents one request and consists of space delimited fields. The following is an example log consisting of six log records.

```json
79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be mybucket [06/Feb/2014:00:00:38 +0000] 192.0.2.3 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be 3E57427F3EXAMPLE REST.GET.VERSIONING - "GET /mybucket?versioning HTTP/1.1" 200 - 113 - 7 - "-" "S3Console/0.4" - 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be mybucket [06/Feb/2014:00:00:38 +0000] 192.0.2.3 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be 891CE47D2EXAMPLE REST.GET.LOGGING_STATUS - "GET /mybucket?logging HTTP/1.1" 200 - 242 - 11 - "-" "S3Console/0.4" - 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be mybucket [06/Feb/2014:00:00:38 +0000] 192.0.2.3 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be A1206F460EXAMPLE REST.GET.BUCKETPOLICY - "GET /mybucket?policy HTTP/1.1" 404 NoSuchBucketPolicy 297 - 38 - "-" "S3Console/0.4" - 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be mybucket [06/Feb/2014:00:01:00 +0000] 192.0.2.3 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be 7B4A0FABBEXAMPLE REST.GET.VERSIONING - "GET /mybucket?versioning HTTP/1.1" 200 - 113 - 33 - "-" "S3Console/0.4" - 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be mybucket [06/Feb/2014:00:01:57 +0000] 192.0.2.3 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be DD6CC733AEXAMPLE REST.PUT.OBJECT s3-dg.pdf "PUT /mybucket/s3-dg.pdf HTTP/1.1" 200 - - 4406583 41754 28 "-" "S3Console/0.4" - 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be mybucket [06/Feb/2014:00:03:21 +0000] 192.0.2.3 79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be BC3C074D0EXAMPLE REST.GET.VERSIONING - "GET /mybucket?versioning HTTP/1.1" 200 - 113 - 28 - "-" "S3Console/0.4" -
```

### Sample Query

```sql
| parse "* * [*] * * * * * \"* HTTP/1.1\" * * * * * * * \"*\" *" as bucket_owner, bucket, time, remoteIP, requester, request_ID, operation, key, request_URI, status_code, error_code, bytes_sent, object_size, total_time, turn_time, referrer, user_agent, version_ID
| parse regex field=operation "[A-Z]+\.(?<operation>[\w.]+)"
| count by operation
```


## Collecting Logs for the Amazon S3 Audit App

Amazon Simple Storage Service (S3) provides a simple web services interface that can be used to store and retrieve any amount of data from anywhere on the web.

This topic details how to collect logs for Amazon S3 Audit and ingest them into Sumo Logic.

Once you begin uploading data, your daily data usage will increase. It's a good idea to check the Account page in  Sumo Logic to make sure that you have enough quota to accommodate additional data in your account. If you need additional quota you can [upgrade your account](/docs/manage/manage-subscription/upgrade-cloud-flex-account.md) at any time.


### Before you begin

Before you can begin to collect logs from an S3 bucket, perform the following steps:

1. [Enable logging](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerLogs.html) via the AWS Management Console.
2. Confirm that logs are being delivered to an S3 bucket.
3. [Grant Sumo Logic Access to the AWS S3 Bucket](/docs/send-data/hosted-collectors/amazon-aws/grant-access-aws-product.md).


### Configure a Collector

In Sumo Logic, configure a [Hosted Collector](/docs/send-data/hosted-collectors/configure-hosted-collector).


### Configure an S3 Audit Source

When you create an AWS Source, you associate it with a Hosted Collector. Before creating the Source, identify the Hosted Collector you want to use, or create a new Hosted Collector. For instructions, see [Configure a Hosted Collector](/docs/send-data/hosted-collectors/configure-hosted-collector).


#### Rules

* If you're editing the `Collection should begin` date on a Source the new date must be after the current `Collection should begin` date.
* Sumo Logic supports log files (S3 objects) that do NOT change after they are uploaded to S3. Support is not provided if your logging approach relies on updating files stored in an S3 bucket. S3 does not have a concept of updating existing files, you can only overwrite an existing file. When this overwrite happens, S3 considers it as a new file object, or a new version of the file, and that file object gets its own unique version ID.

  Sumo Logic scans an S3 bucket based on the path expression supplied, or receives an SNS notification when a new file object is created. As part of this, we receive a file name (key) and the object's ID. It's compared against a list of file objects already ingested. If a matching file ID is not found the contents of the file are ingested in full.

  When you overwrite a file in S3, the file object gets a new version ID and as a result, Sumo Logic sees it as a new file and ingests all of it. If with each version you post to S3 you are simply adding to the end of the file, then this will lead to duplicate messages ingested, one message for each version of the file you created in S3.

* Glacier objects will not be collected and are ignored.
* If you're using SNS you need to create a separate topic and subscription for each Source.


#### S3 Event Notifications Integration

Sumo’s S3 integration combines scan-based discovery and event based discovery into a unified integration that gives you the ability to maintain a low-latency integration for new content and provide assurances that no data was missed or dropped. When you enable event based notifications S3 will automatically publish new files to Amazon Simple Notification Service (SNS) topics which Sumo Logic can be subscribed. This notifies Sumo Logic immediately when new files are added to your S3 bucket so we can collect it. For more information about SNS, see the [Amazon SNS product](https://aws.amazon.com/sns/) detail page.

<img src={useBaseUrl('img/integrations/amazon-aws/Cloud_AWS_icon.png')} alt="icon" />

Enabling event based notifications is an S3 bucket-level operation that subscribes to an SNS topic. An SNS topic is an access point that Sumo Logic can dynamically subscribe to in order to receive event notifications. When creating a Source that collects from an S3 bucket Sumo assigns an endpoint URL to the Source. The URL is for you to use in the AWS subscription to the SNS topic so AWS notifies Sumo when there are new files. See [Configuring Amazon S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/dev/NotificationHowTo.html) for more information.

You can adjust the configuration of when and how AWS handles communication attempts with Sumo Logic. See [Setting Amazon SNS Delivery Retry Policies](https://docs.aws.amazon.com/sns/latest/dg/DeliveryPolicies.html) for details.


#### Create an AWS Source

These configuration instructions apply to log collection from all AWS Source types. Select the correct Source type for your Source in Step 3.

1. In Sumo Logic select** Manage Data > Collection > Collection**.
2. On the **Collectors** page, click **Add Source** next to a Hosted Collector, either an existing Hosted Collector, or one you have created for this purpose.
3. Select your AWS Source type.
4. Enter a name for the new Source. A description is optional.
5. Select an **S3 region** or keep the default value of **Others**. The S3 region must match the appropriate S3 bucket created in your Amazon account.

:::info
Selecting an AWS GovCloud region means your data will be leaving a FedRAMP-high environment. Use responsibly to avoid information spillage. See [Collection from AWS GovCloud](/docs/send-data/hosted-collectors/amazon-aws/collection-aws-govcloud) for details.
:::

6. For **Bucket Name**, enter the exact name of your organization's S3 bucket. Be sure to double-check the name as it appears in AWS, for example:
7. For **Path Expression**, enter the wildcard pattern that matches the S3 objects you'd like to collect. You can use **one **wildcard (*) in this string. Recursive path expressions use a single wildcard and do **NOT** use a leading forward slash. [See About Amazon Path Expressions](/docs/send-data/hosted-collectors/amazon-aws/Amazon-Path-Expressions) for details.
8. **Collection should begin.** Choose or enter how far back you'd like to begin collecting historical logs. You can either:
    * Choose a predefined value from dropdown list, ranging from "Now" to “72 hours ago” to “All Time”, or
    * Enter a relative value. To enter a relative value, click the **Collection should begin** field and press the delete key on your keyboard to clear the field. Then, enter a relative time expression, for example `-1w`. You can define when you want collection to begin in terms of months (M), weeks (w), days (d), hours (h), and minutes (m). If you paused the Source and want to skip some data when you resume, update the **Collection should begin** setting to a time after it was paused.
9. For **Source Category**, enter any string to tag the output collected from this Source. (Category metadata is stored in a searchable field called `_sourceCategory`.)
10. **Fields**. Click the **+Add Field** link to add custom log metadata [Fields](/docs/manage/fields.md). Then define the fields you want to associate, each field needs a name (key) and value.
    * ![green check circle.png](/img/reuse/green-check-circle.png) A green circle with a check mark is shown when the field exists and is enabled in the Fields table schema.
    * ![orange exclamation point.png](/img/reuse/orange-exclamation-point.png) An orange triangle with an exclamation point is shown when the field doesn't exist, or is disabled, in the Fields table schema. In this case, an option to automatically add or enable the nonexistent fields to the Fields table schema is provided. If a field is sent to Sumo that does not exist in the Fields schema or is disabled it is ignored, known as dropped.
11. For **AWS Access** you have two **Access Method** options. Select **Role-based access** or **Key access** based on the AWS authentication you are providing. Role-based access is preferred, this was completed in the prerequisite step [Grant Sumo Logic access to an AWS Product](/docs/send-data/hosted-collectors/amazon-aws/grant-access-aws-product.md).
    * For **Role-based access** enter the Role ARN that was provided by AWS after creating the role.  \
    * For **Key access** enter the **Access Key ID** and **Secret Access Key**. See [AWS Access Key ID](http://docs.aws.amazon.com/STS/latest/UsingSTS/UsingTokens.html#RequestWithSTS) and [AWS Secret Access Key](https://aws.amazon.com/iam/) for details.
12. **Log File Discovery.** You have the option to set up Amazon Simple Notification Service (SNS) to notify Sumo Logic of new items in your S3 bucket. A scan interval is required and automatically applied to detect log files.
    * **Scan Interval.** Sumo Logic will periodically scan your S3 bucket for new items in addition to SNS notifications. **Automatic** is recommended to not incur additional AWS charges. This sets the scan interval based on if subscribed to an SNS topic endpoint and how often new files are detected over time. If the Source is not subscribed to an SNS topic and set to **Automatic** the scan interval is 5 minutes. You may enter a set frequency to scan your S3 bucket for new data. To learn more about Scan Interval considerations, see [About setting the S3 Scan Interval](/docs/send-data/hosted-collectors/amazon-aws/aws-s3-scan-interval-sources).
    * **SNS Subscription Endpoint (Highly Recommended**). New files will be collected by Sumo Logic as soon as the notification is received. This will provide faster collection versus having to wait for the next scan to detect the new file. Click the box below to open instructions:

    <details><summary>Set up SNS in AWS</summary>

    The following steps use the AWS SNS Console. You may instead use AWS CloudFormation. Follow the instructions to use [CloudFormation to set up an SNS Subscription Endpoint](/docs/send-data/hosted-collectors/amazon-aws/configure-your-aws-source-cloudformation#Set-up-an-SNS-Subscription-Endpoint).

    1. To set up the subscription you need to get an endpoint URL from Sumo to provide to AWS. This process will save your Source and begin scanning your S3 bucket when the endpoint URL is generated. Click **Create URL** and use the provided endpoint URL when creating your subscription in step 3.

     Sumo Logic highly recommends using an SNS Subscription Endpoint for its ability to maintain low-latency collection. This is essential to support up-to-date [Alerts](/docs/alerts).

    2. Go to **Services** > **Simple Notification Service** and click **Create Topic**. Enter a **Topic name** and click **Create topic**. Copy the provided **Topic ARN**, you’ll need this for the next step. Make sure that the topic and the bucket are in the same region.
    3. Again go to **Services >** **Simple Notification Service** and click **Create Subscription**. Paste the **Topic ARN** from step 2 above. Select **HTTPS** as the protocol and enter the **Endpoint** URL provided while creating the S3 source in Sumo Logic. Click **Create subscription** and a confirmation request will be sent to Sumo Logic. The request will be automatically confirmed by Sumo Logic.
    4. Select the **Topic** created in step 2 and navigate to **Actions > Edit Topic Policy**. Use the following policy template, replace the `SNS-topic-ARN` and `bucket-name` placeholders in the `Resource` section of the JSON policy with your actual SNS topic ARN and S3 bucket name:
    ```json
    {
        "Version": "2008-10-17",
        "Statement": [{
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "SNS:Publish"
            ],
            "Resource": "SNS-topic-ARN",
            "Condition": {
                "ArnLike": {
                    "aws:SourceArn": "arn:aws:s3:*:*:bucket-name"
                }
            }
        }]
    }
    ```
    5. Go to Services > [S3](https://s3.console.aws.amazon.com/s3/buckets/) and select the bucket to which you want to attach the notifications. Navigate to **Properties > Events > Add Notification**. Enter a **Name** for the event notification. In the **Events** section select **All object create events**. In the **Send to** section (notification destination) select **SNS Topic**. An **SNS** section becomes available, select the name of the topic you created in step 2 from the dropdown. Click **Save**.

    </details>

13. Set any of the following under **Advanced**:
  * **Enable Timestamp Parsing.** This option is selected by default. If it's deselected, no timestamp information is parsed at all.

    * **Time Zone.** There are two options for Time Zone. You can use the time zone present in your log files, and then choose an option in case time zone information is missing from a log message. Or, you can have Sumo Logic completely disregard any time zone information present in logs by forcing a time zone. It's very important to have the proper time zone set, no matter which option you choose. If the time zone of logs can't be determined, Sumo Logic assigns logs UTC; if the rest of your logs are from another time zone your search results will be affected.
    * **Timestamp Format.** By default, Sumo Logic will automatically detect the timestamp format of your logs. However, you can manually specify a timestamp format for a Source. See [Timestamps, Time Zones, Time Ranges, and Date Formats](/docs/send-data/reference-information/time-reference) for more information.
* **Enable Multiline Processing. **See [Collecting Multiline Logs](/docs/send-data/reference-information/collect-multiline-logs) for details on multiline processing and its options. This is enabled by default. Use this option if you're working with multiline messages (for example, log4J or exception stack traces). Deselect this option if you want to avoid unnecessary processing when collecting single-message-per-line files (for example, Linux system.log). Choose one of the following:  
    * **Infer Boundaries.** Enable when you want Sumo Logic to automatically attempt to determine which lines belong to the same message. If you deselect the Infer Boundaries option, you will need to enter a regular expression in the Boundary Regex field to use for detecting the entire first line of multiline messages.
    * **Boundary Regex.** You can specify the boundary between messages using a regular expression. Enter a regular expression that matches the entire first line of every multiline message in your log files.
14. [Create any Processing Rules](/docs/send-data/collection/processing-rules/create-processing-rule.md) you'd like for the AWS Source.
15. When you are finished configuring the Source, click **Save**.

#### SNS with one bucket and multiple Sources

When collecting from one AWS S3 bucket with multiple Sumo Sources you need to create a separate topic and subscription for each Source. Subscriptions and Sumo Sources should both map to only one endpoint. If you were to have multiple subscriptions Sumo would collect your objects multiple times.

Each topic needs a separate filter (prefix/suffix) so that collection does not overlap. 


#### Update Source to use S3 Event Notifications

There is a [community supported script](https://github.com/SumoLogic/sumologic-content/tree/master/Sumo-Logic-Tools/Event_Based_S3_Automation) available that configures event based object discovery on existing AWS Sources.

1. In Sumo Logic select **Manage Data > Collection > Collection**.
2. On the Collection page navigate to your Source and click **Edit**. Scroll down to **Log File Discovery** and note the Endpoint **URL** provided, you will use this in step 10.C when creating your subscription.
3. Complete steps 10.B through 10.E for [configuring SNS Notifications](#Configure-SNS-Notifications).


#### Troubleshooting S3 Event Notifications

In the web interface under **Log File Discovery** it shows a red exclamation mark with "Sumo Logic has not received a validation request from AWS".

Steps to troubleshoot:

1. Refresh the Source’s page to view the latest status of the subscription in the SNS Subscription section by clicking **Cancel** then **Edit** on the Source in the Collection tab.
2. Verify you have enabled sending **Notifications** from your S3 bucket to the appropriate SNS topic. This is done in [step 10.E](#Configure-SNS-Notifications).
3. If you didn’t use CloudFormation check that the SNS topic has a confirmed subscription to the URL in AWS console. A "Pending Confirmation" state likely means that you entered the wrong URL while creating the subscription.

In the web interface under **Log File Discovery** it shows a green check with "Sumo Logic has received an AWS validation request at this endpoint." but you still have high latencies.

The green check confirms that the endpoint was used correctly, but it does not mean Sumo is receiving notifications successfully.

Steps to troubleshoot:

1. AWS writes CloudTrail and S3 Audit Logs to S3 with a latency of a few minutes. If you’re seeing latencies of around 10 minutes for these Sources it is likely because AWS is writing them to S3 later than expected.
2. Verify you have enabled sending **Notifications** from your S3 bucket to the appropriate SNS topic. This is done in [step 10](#Configure-SNS-Notifications).


### Field Extraction Rules

Field Extraction Rules (FERs) tell Sumo Logic which fields to parse out automatically. For instructions, see [Create a Field Extraction Rule](/docs/manage/field-extractions/create-field-extraction-rule).

Use the following Parse Expression:

```sql
parse "* * [*] * * * * * \"* HTTP/1.1\" * * * * * * * \"*\" *" as bucket_owner, bucket, time, remoteIP, requester, request_ID, operation, key, request_URI, status_code, error_code, bytes_sent, object_size, total_time, turn_time, referrer, user_agent, version_ID
```



## Installing the Amazon S3 Audit App

Now that you have configured log collection for Amazon S3 Audit, install the Sumo Logic App for Amazon S3, and take advantage of predefined Searches and [dashboards](#viewing-dashboards). The Sumo Logic App for Amazon S3 Audit presents details from access logs that contain information about the request type, the average response time, and the inbound and outbound data volume.

Our new app install flow is now in Beta. It is only enabled for certain customers while we gather Beta customer feedback. If you can see the Add Integration button, follow the "Before you begin" section in the "Collect Logs" help page and then use the in-product instructions in Sumo Logic to set up the app.

To install the app:

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.

1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.

Version selection is applicable only to a few apps currently. For more information, see the [Install the Apps from the Library.](/docs/get-started/apps-integrations#install-apps-from-the-library)

3. To install the app, complete the following fields.
   * **App Name.** You can retain the existing name, or enter a name of your choice for the app. 
   * **Data Source.** Select either of these options for the data source. 
      * Choose **Source Category**, and select a source category from the list. 
      * Choose **Enter a Custom Data Filter**, and enter a custom source category beginning with an underscore. Example: (`_sourceCategory=MyCategory`). 
   * **Advanced**. Select the **Location in Library** (the default is the Personal folder in the library), or click **New Folder** to add a new folder.
4. Click **Add to Library**.

Once an app is installed, it will appear in your **Personal** folder, or other folder that you specified. From here, you can share it with your organization.

Panels will start to fill automatically. It's important to note that each panel slowly fills with data matching the time range query and received since the panel was created. Results won't immediately be available, but with a bit of time, you'll see full graphs and maps.


## Viewing Amazon S3 Audit Dashboards

### Overview

This dashboard dashboard provides the geolocation of S3 operations, requests performed, data volume sent, and total requests by S3 buckets.

<img src={useBaseUrl('img/integrations/amazon-aws/S3-Overview.png')} alt="S3 Audit dashboards" />

**Geolocation of Clients.** Performs a geo lookup operation and displays the location of S3 bucket clients and the number of requests per bucket on a map of the world for the last three hours.

**Requests by Operation.** Displays the requests performed for the S3 bucket in a pie chart listed by operation type in a legend for the last three hours.

**Data Volume Sent in MB by S3 Bucket.** Shows the data volume per S3 bucket in megabytes, displayed in an bar chart for the last three hours.

**Total Requests by S3 Bucket.** Shows the total requests per S3 bucket, displayed in an bar chart for the last three hours.


### Details

This dashboard provides geolocation of s3 clients, data added, 4xx/5xx status codes, latency, and requests by S3 buckets.

<img src={useBaseUrl('img/integrations/amazon-aws/S3-Details.png')} alt="S3 Audit dashboards" />


**Geolocation of Clients.** Performs a geo lookup operation and displays the location of S3 bucket clients and the number of requests per bucket on a map of the world for the last three hours.

**Data Volume Sent in MB by S3 Bucket.** Shows the data volume per S3 bucket in megabytes, displayed in an area chart on a timeline for the last three hours.

**Total Requests by S3 Bucket.** Shows the total requests per S3 bucket, displayed in an area chart on a timeline for the last three hours.

**Data Added to S3 Bucket.** Lists the connected S3 bucket name and displays the amount of data loaded per bucket in megabytes in an aggregation table for the last three hours.

**Requests by Operation.** Displays the requests performed for the S3 bucket in a pie chart listed by operation type in a legend for the last three hours.

**Total 4xx/5xx Status codes by S3 Bucket.** Lists the total 4xx or 5xx error status codes by S3 bucket in a stacked column chart on a timeline for the last three hours.

**Average Latency in Milliseconds by S3 Bucket.** Displays the average latency time per S3 bucket in milliseconds in an area chart on a timeline for the last three hours.


### Threat Intel

This dashboard provides high-level views of threats throughout your S3 Service. Dashboard panels display visual graphs and detailed information on Threats by Client IP, Threats by Actors, and Threat by Malicious Confidence.

<img src={useBaseUrl('img/integrations/amazon-aws/S3-Threat-Intel.png')} alt="S3 Audit dashboards" />
