# Using third-party log management services to manage logs.

The default Cloud Foundry logging system stores a limited amount of logging information for a defined time.

Elasticsearch [external link], Logstash [external link] and Kibana [external link] (E.L.K.) work together as a one-stop-shop to streamline logging. They are, respectively:

- a search and analytics engine
- a server‑side data processing pipeline that ingests data from multiple sources simultaneously. It transforms it using proprietary technology, sends it to a "stash" like Elasticsearch.
- a program that lets users visualize data with charts and graphs in Elasticsearch

[IMAGE]

You can retain logging information for a longer period of time by setting up a third party log management service. Instructions on how to set up a log management service can be found [here](http://here) [external link]. The next section details how to set up an example log management service, [Logit.io](https://logit.i/).

## Set up the Logit.io log management service

These instructions assume that you already have your app set up and running on CF, and have set up [Logit](http://logit.io) [external link] as your log management service.

### Initial steps

1. Go to your Logit.io dashboard.
2. Identify the Logit ELK stack you want to use.
3. Click on the Logit ELK stack that you want to use


This will setup [logstash](https://www.elastic.co/products/logstash) [external link] to process the Cloud Foundry logs into separate [gorouter](https://www.elastic.co/products/logstash) [external link] and application log types.

Copy the Log stash filter code:

```
filter {
    grok {
        # attempt to parse syslog lines
        match => { "message" => "%{SYSLOG5424PRI}%{NONNEGINT:syslog_ver}
      }
    }
```

### Configuring logstash filters

1. Go back to the dash board and select the __Settings__ for the stack you want to use.
1. click Logstash Filters on the __Stack options__ menu
1. Replace the code on this page with the copied logstash filter code.
1. Select __Validate__ button at the bottom of this page.
1. Once the code has been validated, select the __Apply__ button that appear
1. Go back to the dashboard once the following confirmation message appears: _Filters have been applied to logstash, logstash will be restarted, this may take up to 2 minutes.”_
1. Select the __Settings__ button for the stack you want to use
1. Select __Logstash Inputs__ on the Stack options menu.
1. Note your _Stack Logstash_ endpoint and TCP-SSL port
1. Create the log drain service in Cloud Foundry:
    ```
    cf create-user-provided-service logit-ssl-drain -l syslog-tls://ENDPOINT:PORT
    ```
1. bind the service to your app:
    cf bind-service YOUR-CF-APP-NAME logit-ssl-drain
1. Restage your app:
    ```
    cf restage YOUR_CF_APP_NAME
    ```
1. Click __Access Kibana__ on the __Stack options__ menu and verify that you can see the logs in Kibana.

Once you have verified that the logs are draining correctly, you have successfully set up a log management service.

### More about logs and troubleshooting

For more information about logging and troubleshooting app deployment, see the Cloud Foundry documentation.
