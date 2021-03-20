---
title: Azure Event Hubs - | de SDKs de clientes Microsoft Docs
description: Este artigo fornece informações sobre SDKs clientes para Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9f4eec603245f1e4ea6fa4d97b843ca6a770f2ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930881"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs - SDKs de clientes
Este artigo fornece as seguintes informações para os SDKs apoiados por Azure Event Hubs: 

- Localização do pacote que pode utilizar nas suas aplicações 
- Localização do GitHub onde você pode encontrar código fonte, amostras, ler, alterar registo, problemas relatados, e também levantar novos problemas 
- Links para tutoriais de arranque rápido 

## <a name="client-sdks"></a>SDKs do Cliente
A tabela seguinte descreve todos os clientes atualmente disponíveis Azure Event Hubs. Embora algumas destas bibliotecas também incluam funcionalidades de gestão limitadas, existem também bibliotecas específicas dedicadas a operações de gestão. O foco principal destas bibliotecas é **enviar e receber mensagens** de um centro de eventos.

| Linguagem | Pacote | Referência | 
| -------- | ------- | --------------- | 
| . Padrão NET **(mais recente** e suporta o quadro .NET Core e .NET) | [Azure.Messaging.EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure.Messaging.EventHubs.Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . Padrão NET **(legado** e suporta quadro .NET Core e .NET) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . Net Framework **(antigo)** | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[Tutorial](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [azure-mensagens-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |
|      | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(legado)** | <ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Tutorial](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Tutorial](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [azure/evento-hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Tutorial](event-hubs-node-get-started-send.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Tutorial](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-event-hubs-go)</li><li>[Tutorial](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[Localização gitHub](https://github.com/Azure/azure-event-hubs-c)</li><li>[Tutorial](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>SDKs de gestão
A tabela que se segue lista todas as bibliotecas específicas da gestão atualmente disponíveis. Nenhuma destas bibliotecas contém operações de tempo de execução, e têm como único propósito **gerir entidades do Event Hubs.**

| Linguagem | Pacote | Referência | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[Localização gitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs](./event-hubs-about.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
