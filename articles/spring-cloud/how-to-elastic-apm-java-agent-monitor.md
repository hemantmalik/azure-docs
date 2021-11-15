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

* Monitor apps using the Elastic APM Java Agent by using persistent storage with Azure Spring Cloud
* Leverage Diagnostic Settings to ship Azure Spring Cloud logs to Elastic [Analyze logs with Elastic(ELK) using diagnostics settings](https://github.com/hemantmalik/azure-docs/blob/master/articles/spring-cloud/how-to-elastic-diagnostic-settings.md)

> [Introduction to Elastic Observability Solution](https://www.youtube.com/watch?v=uCX24hRBULY)

## Prerequisites

* [Azure CLI](/cli/azure/install-azure-cli)
* [Deploy Elastic on Azure](https://www.elastic.co/blog/getting-started-with-the-azure-integration-enhancement)
* Elastic APM Endpoint and Secret Token from the Elastic Deployment ](https://www.elastic.co/guide/en/cloud/current/ec-manage-apm-and-fleet.html)

## Monitor Azure Spring Cloud Applications using Elastic APM

The following sections use Spring Petclinic service as an example to walk through the steps of how to activate Elastic APM Java agent for your Azure Spring Cloud Applications using the persistent storage feature.

### Deploy Spring Petclinic Application
1. Follow the guide [here](https://github.com/Azure-Samples/spring-petclinic-microservices) to deploy Microservices based Spring Petclinic application to Azure Spring Cloud. Follow this guide until the [Deploy Spring Boot applications and set environment variables](https://github.com/Azure-Samples/spring-petclinic-microservices#deploy-spring-boot-applications-and-set-environment-variables) step.
   
### Enable custom persistent storage for  Azure Spring Cloud service
2. Follow the steps [here ](https://docs.microsoft.com/en-us/azure/spring-cloud/how-to-custom-persistent-storage) to enable your custom persistent storage.
3. For Step 3 in the above documentation follow the below steps to add persistent storage for Petclinic apps.
   ```bash
   #API_Gateway
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name asc-elastic --mount-path "/elastic/apm/api-gateway" --storage-name "asc-elastic-storage" -n ${API_GATEWAY} -g ${RESOURCE_GROUP} -s ${SPRING_CLOUD_SERVICE}
   
   #ADMIN_SERVER
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name asc-elastic --mount-path "/elastic/apm/admin-server" --storage-name "asc-elastic-storage" -n ${ADMIN_SERVER} -g ${RESOURCE_GROUP} -s ${SPRING_CLOUD_SERVICE}
   
   #CUSTOMERS_SERVICE
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name asc-elastic --mount-path "/elastic/apm/customers-service" --storage-name "asc-elastic-storage" -n ${CUSTOMERS_SERVICE} -g ${RESOURCE_GROUP} -s ${SPRING_CLOUD_SERVICE}
   
   #VETS_SERVICE
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name asc-elastic --mount-path "/elastic/apm/vets-service" --storage-name "asc-elastic-storage" -n ${VETS_SERVICE} -g ${RESOURCE_GROUP} -s ${SPRING_CLOUD_SERVICE}
   
   #VISITS_SERVICE
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name asc-elastic --mount-path "/elastic/apm/visits-service" --storage-name "asc-elastic-storage" -n ${VISITS_SERVICE} -g ${RESOURCE_GROUP} -s ${SPRING_CLOUD_SERVICE}```


### Activate Elastic APM Java Agent

1. Before proceeding ahead you would need Elastic APM server connectivity information handy. This assumes you have completed  [Deployment of Elastic on Azure](https://www.elastic.co/blog/getting-started-with-the-azure-integration-enhancement).

2. From Azure Portal, click on the Manage Elastic Cloud Deployment on the Overview blade  of your Elastic Deployment.

![Go to Elastic Cloud ]()
3. Under your deployment on Elastic Cloud Console, click on APM & Fleet section to get Elastic APM Server endpoint and secret token

![Elastic Cloud - Get APM Endpoint and token ]()

4. Once you have the Elastic APM endpoint and secret token, follow the following commands to deploy the applications. Replace the Elastic APM Server URL and Secret Token with your values.

```bash
#API_GATEWAY
az spring-cloud app deploy --name ${API_GATEWAY} \
       --artifact-path ${API_GATEWAY_JAR} \
        --jvm-options='-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql -javaagent:/elastic/apm/api-gateway/elastic-apm-agent-1.26.0.jar  -Delastic.apm.service_name=API_GATEWAY -Delastic.apm.application_packages=org.springframework.samples.petclinic  -Delastic.apm.server_url=<replace-with-your-Elastic-APM-server-url> -Delastic.apm.secret_token=<replace-with-your-Elastic-APM-secret-token>'


#ADMIN_SERVER
az spring-cloud app deploy --name ${ADMIN_SERVER} \
        --artifact-path ${ADMIN_SERVER_JAR} \
        --jvm-options='-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql -javaagent:/elastic/apm/admin-server/elastic-apm-agent-1.26.0.jar  -Delastic.apm.service_name=ADMIN-SERVER -Delastic.apm.application_packages=org.springframework.samples.petclinic  -Delastic.apm.server_url=<replace-with-your-Elastic-APM-server-url> -Delastic.apm.secret_token=<replace-with-your-Elastic-APM-secret-token>'


#CUSTOMERS_SERVICE
az spring-cloud app deploy --name ${CUSTOMERS_SERVICE} \
   --artifact-path ${CUSTOMERS_SERVICE_JAR} \
   --jvm-options='-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql -javaagent:/elastic/apm/customers-service/elastic-apm-agent-1.26.0.jar  -Delastic.apm.service_name=CUSTOMERS-SERVICE -Delastic.apm.application_packages=org.springframework.samples.petclinic  -Delastic.apm.server_url=<replace-with-your-Elastic-APM-server-url> -Delastic.apm.secret_token=<replace-with-your-Elastic-APM-secret-token>' \
   --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
         MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
         MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
         MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
    
#VETS_SERVICE
az spring-cloud app deploy --name ${VETS_SERVICE} \
   --artifact-path ${VETS_SERVICE_JAR} \
   --jvm-options='-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql -javaagent:/elastic/apm/vets-service/elastic-apm-agent-1.26.0.jar  -Delastic.apm.service_name=VETS-SERVICE -Delastic.apm.application_packages=org.springframework.samples.petclinic  -Delastic.apm.server_url=<replace-with-your-Elastic-APM-server-url> -Delastic.apm.secret_token=<replace-with-your-Elastic-APM-secret-token>' \
   --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
         MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
         MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
         MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
              
#VISITS_SERVICE
az spring-cloud app deploy --name ${VISITS_SERVICE} \
   --artifact-path ${VISITS_SERVICE_JAR} \
   --jvm-options='-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql -javaagent:/elastic/apm/visits-service/elastic-apm-agent-1.26.0.jar  -Delastic.apm.service_name=VISITS-SERVICE -Delastic.apm.application_packages=org.springframework.samples.petclinic  -Delastic.apm.server_url=<replace-with-your-Elastic-APM-server-url> -Delastic.apm.secret_token=<replace-with-your-Elastic-APM-secret-token>' \
   --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
         MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
         MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
         MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}

```

### Monitoring Applications and metrics with Elastic APM

1. From the Azure Portal, click on Kibana link from the overview blade of Elastic Deployment to open Kibana. 
   
   ![Open Kibana from Azure]()

2. Once Kibana is open, search for APM in the search bar and go to APM.
   
   ![Open APM in Kibana]()

3. 