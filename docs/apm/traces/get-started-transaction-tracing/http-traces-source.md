---
id: http-traces-source
title: Configure an HTTP Traces Source
description: An HTTP Traces Source is an endpoint for receiving traces.
---

An HTTP Traces Source is an endpoint for receiving trace data. The URL securely encodes the Collector and Source information. You can add as many HTTP Traces Sources as you'd like to a single Hosted Collector.

When you set up an HTTP Traces Source, a unique URL is assigned to that Source. The generated URL is a long string of letters and numbers. You can generate a new URL at any time.

## Limitations

* [Zipkin JSON v2](https://zipkin.io/zipkin-api/) and [OTLP/HTTP](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/protocol/otlp.md#otlphttp) are the only supported formats.
* Traces up to 1,000 spans are supported, this includes metadata. Traces above 1,000 spans per trace may be throttled and/or the critical path by service may be inaccurate.

## Create an HTTP Traces Source

To configure an HTTP Traces Source:

1. In the Sumo Logic web interface, select **Manage Data > Collection > Collection**. 
1. On the Collection page, click **Add Source** next to a Hosted Collector.
1. Select **HTTP Traces**. <br/>  ![http traces.png](/img/traces/http-traces.png)
1. Enter a **Name** for the Source. A description is optional. <br/>    ![traces source no fields.png](/img/traces/traces-source-no-fields.png)
1. (Optional) For **Source Host** and **Source Category**, enter any string to tag the output collected from the source. These are [built-in metadata](/docs/search/get-started-with-search/search-basics/built-in-metadata) fields that allow you to organize your data.
1. When you are finished configuring the Source, click **Save**.

## View the endpoint URL

If you need to access the Source's URL again, click **Show URL**.  <br/>   ![show url traces.png](/img/traces/show-url-traces.png)

## Send a test trace to Sumo

Now, that you have created an HTTP Traces Source you can send a trace to it with
an HTTP POST request. The request's body should look like the following in the Zipkin v2 format
and you can simply send it to the source URL that you created in the previous step.

```
[
  {
    "id": {{parentSpanId}},
    "traceId": {{traceId}},
    "name": "metrics",
    "timestamp": {{timestamp0}},
    "duration": 2000000,
    "kind": "SERVER",
    "localEndpoint": {
      "serviceName": "test_service"
    },
    "tags": {
      "customTag": "tag"
    }
  },
  {
    "id": "352bff9a74ca9c1a",
    "traceId": {{traceId}},
    "parentId": {{parentSpanId}},
    "name": "metricsMethod",
    "timestamp": {{timestamp1}},
    "duration": 1000000,
    "kind": "SERVER",
    "localEndpoint": {
      "serviceName": "test_service2"
    },
    "tags": {
      "customTag": "tag"
    }
  },
  {
    "id": "352bff9a74ca9b1e",
    "traceId": {{traceId}},
    "parentId": {{parentSpanId}},
    "name": "metricsMethod2",
    "timestamp": {{timestamp2}},
    "duration": 1000000,
    "kind": "SERVER",
    "localEndpoint": {
      "serviceName": "test_service3"
    },
    "tags": {
      "customTag": "tag"
    }
  }
]
```
In the example above we have three spans, one parent span without `parentId` and its two children spans
which refers to its `id` with the `parentId` field.

You can replace the variables `parentSpanId` and `traceId` with random hexadecimal ids 
like `462bdf9a74ca9e2c` and `872cef9a74ca9c3a`. You can replace `timestamps` with the current epoch timestamp
in microseconds, for example `1671096513000000`.
