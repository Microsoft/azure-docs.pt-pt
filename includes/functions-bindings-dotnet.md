---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/23/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: a7ed3be6f3161b2152f88956b3eafe92232043aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661171"
---
A tabela seguinte lista os atributos de enlace que estão disponíveis no projeto da biblioteca de classe das funções do Azure e o acionador. Todos os atributos estão no espaço de nomes `Microsoft.Azure.WebJobs`.

| Acionador | Input | Saída|
|------   | ------    | ------  |
| [BlobTrigger](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---attributes)| [Blob](../articles/azure-functions/functions-bindings-storage-blob.md#input---attributes)| [Blob](../articles/azure-functions/functions-bindings-storage-blob.md#output---attributes)|
| [CosmosDBTrigger](../articles/azure-functions/functions-bindings-cosmosdb.md#trigger---attributes)| [DocumentDB](../articles/azure-functions/functions-bindings-cosmosdb.md#input---attributes)| [DocumentDB](../articles/azure-functions/functions-bindings-cosmosdb.md#output---attributes) |
| [EventHubTrigger](../articles/azure-functions/functions-bindings-event-hubs.md#trigger---attributes)|| [EventHub](../articles/azure-functions/functions-bindings-event-hubs.md#output---attributes) |
| [HTTPTrigger](../articles/azure-functions/functions-bindings-http-webhook.md#trigger---attributes)|||
| [QueueTrigger](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---attributes)|| [fila](../articles/azure-functions/functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](../articles/azure-functions/functions-bindings-service-bus.md#trigger---attributes)|| [Barramento de serviço](../articles/azure-functions/functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](../articles/azure-functions/functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](../articles/azure-functions/functions-bindings-external-file.md)| [ApiHubFile](../articles/azure-functions/functions-bindings-external-file.md)|
| |[MobileTable](../articles/azure-functions/functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](../articles/azure-functions/functions-bindings-mobile-apps.md#output---attributes) | 
| |[Tabela](../articles/azure-functions/functions-bindings-storage-table.md#input---attributes)| [Tabela](../articles/azure-functions/functions-bindings-storage-table.md#output---attributes)  | 
| ||[NotificationHub](../articles/azure-functions/functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](../articles/azure-functions/functions-bindings-twilio.md#attributes)| 