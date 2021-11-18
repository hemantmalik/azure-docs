---
title: Analyze logs with Elastic Cloud from Azure Spring Cloud | Microsoft Docs
description: Learn how to analyze diagnostics logs in Azure Spring Cloud using Elastic
author: hemantmalik
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: karler
ms.custom: devx-track-java
---

# Analyze logs with Elastic(ELK) using diagnostics settings 

**This article applies to:** ✔️ Java ✔️ C#

Using the diagnostics functionality of Azure Spring Cloud, you can analyze logs with Elastic (ELK).


## Configure diagnostics settings

1. In the Azure portal, go to your Azure Spring Cloud instance.
2. Select **diagnostics settings** option, and then select **Add diagnostics setting**.
3. Enter a name for the setting, and then choose **Send to partner solution** , select **Elastic** and an Elastic deployment where you want to send the logs.
4. Select **Save**.

   ![Diagnostics Setting](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/diagnostic-settings-asc-2.png)

> [!NOTE]
> 1. There might be a gap of up to 15 minutes between when logs  are emitted and when they appear in your Elastic deployment.
> 1. If the Azure Spring Cloud instance is deleted or moved, the operation will not cascade to the **diagnostics settings** resources. The **diagnostics settings** resources have to be deleted manually before the operation against its parent, the Azure Spring Cloud instance. Otherwise, if a new Azure Spring Cloud instance is provisioned with the same resource ID as the deleted one, or if the Azure Spring Cloud instance is moved back, the previous **diagnostics settings** resources continue extending it.

## Analyze the logs with Elastic

To learn more about deploying Elastic on Azure, see [How to deploy and manage Elastic on Microsoft Azure](https://www.elastic.co/blog/getting-started-with-the-azure-integration-enhancement).

1. From the Elastic deployment overview page in the Azure portal, open **Kibana**.

   ![Deployment Overview](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-on-azure-native-Microsoft-Azure.png)

3. In Kibana in the Search bar at top type **Spring Cloud type:dashboard**

   ![Kibana Search](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-kibana-spring-cloud-dashboard.PNG)

5. Click on the **[Logs Azure] Azure Spring Cloud logs Overview** from the results

   ![ASC Dashboard](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-kibana-asc-dashboard-full.png)

7. You can search on out of the box Azure Spring Cloud dashboards by using the queries such as

   **azure.springcloudlogs.properties.app_name : "visits-service"** 



## Analyze the logs with Kibana Query Language in Discover

Application logs provide critical information and verbose logs about your application's health, performance, and more. In the next sections are some simple queries to help you understand your application's current and past states.

1. In Kibana in the Search bar at top type **Disover**, and click on the result 

   ![Elastic Kibana Discover](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-kibana-go-discover.PNG)

2. In **Discover** app select **logs-** index pattern if it's not already selected. 

   ![Discover Index Pattern](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/discover-index-pattern.png)
 
To learn more about different queries , see [Guide to Kibana Query Language](https://www.elastic.co/guide/en/kibana/current/kuery-query.html).


### Show all logs from Azure Spring Cloud

To review a list of application logs from Azure Spring Cloud, sorted by time with the most recent logs shown first, run the following query in the **Search** box:

```azure_log_forwarder.resource_type : "Microsoft.AppPlatform/Spring" ```

![All Azure Spring Cloud Logs](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/logs_from_azure_spring_cloud.PNG)

### Show specific Log types from Azure Spring Cloud

To review a list of application logs from Azure Spring Cloud, sorted by time with the most recent logs shown first, run the following query in the **Search** box:

```azure.springcloudlogs.category : "ApplicationConsole"```

![Specific Log Types](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-kibana-kql-asc-appconsole.png)


### Show logs entries containing errors or exceptions

To review unsorted log entries that mention an error or exception, run the following query:

```azure_log_forwarder.resource_type : "Microsoft.AppPlatform/Spring" and (log.level : "ERROR" or log.level : "EXCEPTION")```

![Log entries with errors and exceptions](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/error_exception_query.PNG)

You would notice as you start typing in that Kibana Query Language helps users form queries with autocomplete and suggestions to make it easy for users to gain insights from the logs.
Use this query to find errors, or modify the query terms to find specific error codes or exceptions.


### Show  log entries from a specific service

To review log entries that are generated by a specific service, run the following query:

```azure.springcloudlogs.properties.service_name : "petclinic-spring--service"```

![Log entries from a specific service](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-kibana-kql-specific-service.png)

### Show  Config Server Logs containing warnings or errors

To review logs from Config Server, run the following query:

```azure.springcloudlogs.properties.type : "ConfigServer" and (log.level : "ERROR" or log.level : "WARN")```

![Config Server logs containing warnings or errors](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-kibana-kql-config-error-exception.png)

### Show  Service Registry Logs

To review logs from Config Server, run the following query:

```azure.springcloudlogs.properties.type : "ServiceRegistry"```

![Service Registry Logs](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-kibana-kql-service-registry.png)


## Visualizing logs from Azure Spring Cloud with Elastic

### Show the various log levels in your logs to assess overall health of the services

Kibana allows you to visualize  data with Dasbhoards and a rich ecosystem of visualizations. To learn more go to [Dasbhoard and Visualization](https://www.elastic.co/guide/en/kibana/current/dashboard.html)


1. From the available fields list on left in **Disover**, search for log.level in the search box under **logs-** index pattern 

2. Click on **log.level** field. From the floating informational panel about **log.level**, click on **Visualize**
   ![Discover to Visualize](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-kibana-asc-visualize.png)
   
4. From here you can choose to add more data from the left pane, or choose from multiple suggestions how you would like to visualize your data
   ![Discover to Visualize](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/media/elastic-kibana-visualize-lens.png)   
   
## Next steps

* [Quickstart: Deploy your first Spring Boot app in Azure Spring Cloud](./quickstart.md)
* [Deploy Elastic on Azure](https://www.elastic.co/blog/getting-started-with-the-azure-integration-enhancement)
