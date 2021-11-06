---
title:  "How to monitor Spring Boot apps with Elastic APM Java Agent "
description: How to use Elastic APM Java Agent to monitor Spring Boot applications running in Azure Spring Cloud
author:  hemantmalik
ms.author: asir
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/4/2021
ms.custom: devx-track-java
---

# How to monitor Spring Boot apps with Elastic APM Java Agent

This article explains how to use Elastic APM Agent to monitor Spring Boot applications running in Azure Spring Cloud.

With the Elastic Observability Solution you can achieve unified observability for your Azure Spring Cloud Applications by 

* Monitor apps using the Elastic APM Java Agent with sideloading
* Leverage Diagnostic Settings to ship Azure Spring Cloud logs to Elastic [Point to Diagnostic Settings page](https://www.dynatrace.com/support/help/reference/dynatrace-concepts/access-tokens/)

> [Introduction to Elastic Observability Solution](https://www.youtube.com/watch?v=uCX24hRBULY)



## Prerequisites

* [Azure CLI](/cli/azure/install-azure-cli)
* [Deploy Elastic on Azure](https://www.elastic.co/blog/getting-started-with-the-azure-integration-enhancement)
* Elastic APM Endpoint and Secret Token from the Elastic Deployment

## Sideload Elastic APM Java Agent

The following sections use Spring Petclinic service as an example to walk through the steps of how to sideload and enable Elastic APM Java agent for your Azure Spring Cloud Applications.

Follow steps [here](https://github.com/selvasingh/spring-petclinic-microservices) to deploy Microservices based Spring Petclinic application to Azure Spring Cloud.

### Deploy Spring Petclinic Application

1. Create an instance of Azure Spring Cloud.
2. Create an application that you want to monitor with Elastic by running the following command. Replace the placeholders *\<...>* with your own values.
   ```azurecli
   az spring-cloud app create \
       --resource-group <your-resource-group-name> \
       --service <your-Azure-Spring-Cloud-name> \
       --name <your-application-name> \
       --is-public true 
   ```

