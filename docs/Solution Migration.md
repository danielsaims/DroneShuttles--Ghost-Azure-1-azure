---
title: Mordenization of an on-premises application to an Azure App Service, Traffic Manager, and Azure Database for MySQL
author: daniel
date: 01/02/2022
topic: proof of concept
service: cloud-adoption-framework
subservice: migrate and mordenisation
---

![image (3)f](https://user-images.githubusercontent.com/82387743/151999215-ccecc44d-3cba-4096-93f6-9bf4e9fbe416.png)

## Business drivers


- **Address business growth.** DroneShuttles is growing and releasing new products, and they currently run on their own on-premises systems and infrastructure.
- **Increase Reseliency.** DroneShuttles needs to  remain online even in case of a significant geographical failure
- **Increase agility.** DroneShuttles IT needs to be more responsive to the needs of the business. It must be able to react faster than the changes in the marketplace, to enable success in a global economy. Deployment of the application needs to be automated.
- **Scale.** As the business grows successfully, DroneShuttles IT must provide systems that are able to grow at the same pace. x 4 load
- **Future Proof.** DroneShuttles wants to optimize licensing costs.


## Migration goals

To help determine the best migration method, the DroneShuttles requiremnets where mapped to certain goals:

![image](https://user-images.githubusercontent.com/82387743/151895646-c8a17b07-aa40-4764-a118-9d1feb012a3c.png)


| Requirements | Details |
| --- | --- |
| **Application Availability** | The application in Azure will remain as critical as it is today on-premises. <br><br> It should have performance capabilities of 4 times it's current load <br><br> 
| **Azure** | DroneShuttles wants to move it's application to the cloud, Azure. |
| **DevOps** | DroneShuttles wants to move to a DevOps model that uses Azure DevOps for their builds and release pipelines. |

## Solution design

After pinning down their goals and requirements, presented is a deployment solution. This includes the migration process, including the Azure services that they'll use for the migration.

### Current application assumptions

![image](https://user-images.githubusercontent.com/82387743/151895618-c070a3d1-3b1a-4321-9405-345761e0a4c8.png)

-	Application is built with spring boot and angular, angular is deployed in Apache web server. Spring boot application was deployed in tomcat server. It can be accessed from the intranet and internet. The application relies on Microsoft Active Directory services to authenticate employees.
-	Uses a standard Microsoft SQL Server 2019 database for storing application data. 
-	Stores the images uploaded to or created from the application using network file storage. Is hosted on single instance server.


### Migration Methodology.
With the assessment complete, DroneShuttles needs to identify tools to move its applications, data, and infrastructure to Azure.

A simple **“lift and shift migration” (IAAS)** could have been chosen but given the fact that the requirements of the website and to fully **maximize the potential of the azure cloud capabilities** - the solution focused on also the future and did not see the need for 5 Devops Engineer on the website which will already be using a platform like Ghost that has a SAAS option. So  **PAAS serveless**  option was chosen
The main functionality stays the same with a different architecture; a **revamp**  totally in the cloud. 
It modernizes an application into a resilient, highly scalable, independently deployable architecture.

-	Migrate the front-end website to the Web Apps feature of Azure App Service.
-	Migrate PostgreSQL to Azure Database for PostgreSQL (a managed database service for app development and deployment).


## Proposed architecture

Here's the proposed architecture:

- The web tier application will be hosted using the **ghost blog** platform and built using an **Azure App Service web app** in two Azure regions. 
- The application code will be moved to GitHub, and the Azure App Service web app will be configured for continuous delivery with GitHub.
- Azure App Service will be deployed in both the primary region (`West Europe`) and secondary region (`Central US`).
- Azure Traffic Manager will be set up in front of the two web apps in both regions.
- Traffic Manager will be configured in priority mode to force the traffic through `West Europe`.
- If the Azure app server in `West Europe` goes offline, users can access the failed over application in `Central US`.
- The application database will be migrated to the Azure Database for MySQL service by using Azure Database Migration Service. The on-premises database will be backed up locally, and restored directly to Azure Database for MySQL.
- The database will reside in the primary region (`West Europe`) `.
- The Traffic Manager resource will be deployed in DroneShuttles's infrastructure resource group `droneshuttles`.
- Azure Monitor: Application Insights, provides health and performance monitoring, and diagnostics.
- The on-premises machines in the DroneShuttles datacenter will be decommissioned after the migration is done.


![Untitled Diagram drawio (2)](https://user-images.githubusercontent.com/82387743/151984124-5652b3d1-8ad0-4b02-be87-9e73995c706f.png)

### Basic Architecture Overiew

![image](https://user-images.githubusercontent.com/82387743/151985504-fc79ae70-19c6-4de5-b6d0-a98075293673.png)

  **Data Flow**
  1. User accesses Web Apps from Azure App Service in a browser.
  2. Traffic Manager checks to see which web app is geographically best placed to handle each request.
  3. Application Insights detects issues and analyses usage for your web apps.
  4. Web App connects to SQL Database and Azure Cache for Redis for better performance.
  5. Browser pulls resourcesfrom Azure Content Delivery Network to reduce load time.

### Migration process

DroneShuttles completes the migration process as follows:

1. As a first step, DroneShuttles admins set up the Azure infrastructure, including provisioning Azure App Service, setting up Traffic Manager, and provisioning an Azure Database for MySQL instance.
2. After preparing the Azure infrastructure, they migrate the database by using Azure Database Migration Service.
3. Install ghostblog machine and build web app 
4. After the database is running in Azure, they upload a GitHub private repository for Azure App Service with continuous delivery, and load it with the web application.
5. In the Azure portal, they load the application from GitHub  by running Azure App Service.
6. They tweak DNS settings and configure autoscaling for the application.


![Untitled Diagram drawio (3)](https://user-images.githubusercontent.com/82387743/151995871-eaab057e-6dae-4aaa-9660-cd645d559813.png)



### Azure services

| Service | Description | Cost |
| --- | --- | --- |
| [Azure App Service](https://azure.microsoft.com/services/app-service/) | The service runs and scales applications by using Azure platform as a service (PaaS) for websites. | Pricing is based on the size of the instances and the features required. [Learn more](https://azure.microsoft.com/pricing/details/app-service/windows/). |
| [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | A load balancer that uses Domain Name System (DNS) to direct users to Azure or to external websites and services. | Pricing is based on the number of received DNS queries and the number of monitored endpoints. [Learn more](https://azure.microsoft.com/pricing/details/traffic-manager/). |
| [Azure Database Migration Service](/azure/dms/dms-overview) | Azure Database Migration Service enables seamless migration from multiple database sources to Azure data platforms, with minimal downtime. | Learn about [supported regions](/azure/dms/dms-overview#regional-availability) and [Database Migration Service pricing](https://azure.microsoft.com/pricing/details/database-migration/). |
| [Azure Database for MySQL](/azure/mysql/) | The database is based on the open-source MySQL database engine. It provides a fully managed, enterprise-ready community MySQL database for application development and deployment. | Pricing is based on compute, storage, and backup requirements. [Learn more](https://azure.microsoft.com/pricing/details/mysql/server/). |






## Scenario steps

Here's the DroneShuttles plan for completing the migration:
>
> - **Step 1: Provision Azure App Service**. DroneShuttles will provision web apps in the primary and secondary regions.
> - **Step 2: Set up Traffic Manager**. set up Traffic Manager in front of the web apps, for routing and load balancing traffic.
> - **Step 3: Provision Azure Database for MySQL**. In Azure,  provision an instance of Azure Database for MySQL.
> - **Step 4: Migrate the database**.  migrate the database by using Azure Database Migration Service.
> - **Step 5: Set up GitHub**.  set up a local GitHub repository for the application web sites that was built using *Ghost Blog Platform* and code.
> - **Step 6: Configure the web apps**. They configure the web apps with the DroneShuttles websites.

## Review the deployment

With the application now running, DroneShuttles needs to fully operationalize and secure their new infrastructure.

### Security

DroneShuttles reviews the application to determine any security issues. 
This to ensure that the database traffic can't be hacked. [Learn more](/azure/mysql/howto-configure-ssl).

### Backups

Azure Database for MySQL automatically creates server backups and stores.(/azure/mysql/concepts-backup).

### Licensing and cost optimization

- There are no licensing issues for the PaaS deployment.
- DroneShuttles  use [Azure Cost Management + Billing](/azure/cost-management-billing/cost-management-billing-overview) to ensure that they stay within the budgets established by their IT leadership.

## Review the deployment
DroneShuttles refactored the web application in Azure by building the webapp via Ghost Blog to two Azure App Service web apps. The application  database then will migrated/built to Azure SQL Database.
