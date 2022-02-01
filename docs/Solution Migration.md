---
title: Refactor an on-premises application to an Azure App Service web app and  SQL managed instance
description: Rehosting of an on-premises application by migrating it to an Azure App Service web app and a SQL managed instance.
author: daniel
date: 01/02/2022
topic: proof of concept
service: cloud-adoption-framework
subservice: migrate and mordenisation
---

## Business drivers


- **Address business growth.** DroneShuttles is growing and releasing new features, and they currently run on their on-premises systems and infrastructure.
- **Increase Reseliency.** DroneShuttles needs to  remain online even in case of a significant geographical failure
- **Increase agility.** DroneShuttles IT needs to be more responsive to the needs of the business. It must be able to react faster than the changes in the marketplace, to enable success in a global economy. deployment of the application needs to be automated.
- **Scale.** As the business grows successfully, DroneShuttles IT must provide systems that are able to grow at the same pace. x 4 load
- **Reduce costs.** Contoso wants to minimize licensing costs.

![image](https://user-images.githubusercontent.com/82387743/151895671-90592486-aecc-472a-a59f-41d8f3faa57a.png)


## Migration goals

To help determine the best migration method, the DroneShuttles requiremnets where mapped to certain goals:

![image](https://user-images.githubusercontent.com/82387743/151895646-c8a17b07-aa40-4764-a118-9d1feb012a3c.png)


| Requirements | Details |
| --- | --- |
| **Application** | The application in Azure will remain as critical as it is today on-premises. <br><br> It should have the same performance capabilities as it currently does in VMware. will simply moe 
| **Limitations** | The application consists of an ASP.NET application and a Windows Communication Foundation (WCF) service running on the same VM. They want to spread these components across two web apps using the Azure App Service. |
| **Azure** | DroneShuttles wants to move the application to Azure, but they don't want to run it on VMs. Contoso wants to use Azure PaaS services for both the web and data tiers. |
| **DevOps** | DroneShuttles wants to move to a DevOps model that uses Azure DevOps for their builds and release pipelines. |

## Solution design

After pinning down their goals and requirements, DroneShuttles is presented with designs and reviews a deployment solution. They also identify the migration process, including the Azure services that they'll use for the migration.

### Current application

![image](https://user-images.githubusercontent.com/82387743/151895618-c070a3d1-3b1a-4321-9405-345761e0a4c8.png)
