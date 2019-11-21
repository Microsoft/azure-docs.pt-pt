---
title: Azure Functions Runtime Overview
description: Overview of the Azure Functions Runtime Preview
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226634"
---
# <a name="azure-functions-runtime-overview-preview"></a>Azure Functions Runtime Overview (preview)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

The Azure Functions Runtime (preview) provides a new way for you to take advantage of the simplicity and flexibility of the Azure Functions programming model on-premises. Built on the same open source roots as Azure Functions, Azure Functions Runtime is deployed on-premises to provide a nearly identical development experience as the cloud service.

![Azure Functions Runtime Preview Portal][1]

The Azure Functions Runtime provides a way for you to experience Azure Functions before committing to the cloud. In this way, the code assets you build can then be taken with you to the cloud when you migrate.  The runtime also opens up new options for you, such as using the spare compute power of your on-premises computers to run batch processes overnight. You can also use devices within your organization to conditionally send data to other systems, both on-premises and in the cloud.

The Azure Functions Runtime consists of two pieces:

* Azure Functions Runtime Management Role
* Azure Functions Runtime Worker Role

## <a name="azure-functions-management-role"></a>Azure Functions Management Role

The Azure Functions Management Role provides a host for the management of your Functions on-premises. This role performs the following tasks:

* Hosting of the Azure Functions Management Portal, which is the same one you see in the [Azure portal](https://portal.azure.com). The portal provides a consistent experience that lets you develop your functions in the same way as you would in the Azure portal.
* Distributing functions across multiple Functions workers.
* Providing a publishing endpoint so that you can publish your functions direct from Microsoft Visual Studio by downloading and importing the publishing profile.

## <a name="azure-functions-worker-role"></a>Azure Functions Worker Role

The Azure Functions Worker Roles are deployed in Windows Containers and are where your function code executes.  You can deploy multiple Worker Roles throughout your organization and this option is a key way in which customers can make use of spare compute power.  One example of where spare compute exists in many organizations is machines powered on constantly but not being used for large periods of time.

## <a name="minimum-requirements"></a>Minimum Requirements

To get started with the Azure Functions Runtime, you must have a machine with Windows Server 2016 or Windows 10 Creators Update with access to a SQL Server instance.

## <a name="next-steps"></a>Próximos Passos

Install the [Azure Functions Runtime preview](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
