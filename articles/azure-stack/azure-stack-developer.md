---
title: Develop apps for Azure Stack | Microsoft Docs
description: Learn deployment considerations for prototyping Azure Stack appls on Azure
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: helaw
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9654f164a18d4aa6b1465d0fbdadbf0976dc3713


---
# <a name="develop-for-azure-stack"></a>Develop for Azure Stack
You can get started developing applications today, even if you don't have access to an Azure Stack environment. Because Azure Stack delivers Microsoft Azure services that run in your datacenter, you can use similar tools and processes to develop against Azure Stack as you would with Azure.  With a bit of preparation and guidance from the following topics, you can use Azure to emulate an Azure Stack environment:

* In Azure, you can create Azure Resource Manager templates that are also deployable to Azure Stack.  See [template considerations](azure-stack-develop-templates.md) for guidance on developing your templates to ensure portability.
* There is a delta in service availability and service versioning between Azure and Azure Stack. You can use the [Azure Stack policy module](azure-stack-policy-module.md) to restrict Azure service availability and API versions to what's available in Azure Stack. Constraining available services will help your application rely on services available to Azure Stack.
* The [Azure Stack Quickstart Templates](https://github.com/Azure/AzureStack-QuickStart-Templates) are common scenario examples of how to develop your templates so they can be deployed to both Azure and Azure Stack.




<!--HONumber=Nov16_HO3-->


