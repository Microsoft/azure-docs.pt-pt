---
title: Visão geral da API do Azure Event Hubs [ Microsoft Docs
description: Este artigo fornece uma visão geral das APIs disponíveis (tempo de execução e gestão) para a utilização do serviço Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 08e10996f633d35ffbf946b61937a0e9a76f7227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162230"
---
# <a name="available-event-hubs-apis"></a>ApIs de Hubs de Eventos Disponíveis

Este artigo descreve o conjunto de clientes API disponíveis que pode utilizar para gerir os recursos do Event Hubs.

## <a name="runtime-apis"></a>APIs de tempo de execução

A secção seguinte descreve todos os clientes atualmente disponíveis do Azure Event Hubs. Embora algumas destas bibliotecas também incluam funcionalidades de gestão limitadas, existem também [bibliotecas específicas dedicadas](#management-apis) a operações de gestão. O foco principal destas bibliotecas é enviar e receber mensagens de um centro de eventos.

Para obter mais informações sobre o estado atual de cada biblioteca de tempo de execução, consulte [informações adicionais](#additional-information).

| Idioma/Plataforma | Pacote de cliente | Pacote De Anfitrião de Processador de Eventos | Repositório |
| --- | --- | --- | --- |
| .NET Standard | [Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [Nuget](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/D |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Nó | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/D | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | N/D | N/D | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

O ecossistema .NET tem vários tempos de execução, pelo que existem várias bibliotecas .NET para Centros de Eventos. A biblioteca .NET Standard pode ser executada utilizando o .NET Core ou o .NET Framework, enquanto a biblioteca .NET Framework só pode ser executada num ambiente de enquadramento .NET. Para obter mais informações sobre as versões .NET Framework, consulte [versões-quadro](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Nó

A [biblioteca JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) encontra-se atualmente em pré-visualização e é mantida como um projeto paralelo por funcionários da Microsoft e colaboradores externos. Todas as contribuições, incluindo o código fonte, são bem-vindas e serão revistas.

## <a name="management-apis"></a>APIs de gestão

A tabela que se segue lista todas as bibliotecas específicas da gestão atualmente disponíveis. Nenhuma destas bibliotecas contém operações de tempo de execução, e têm como único propósito a gestão de entidades de Hubs de Eventos.

| Idioma/Plataforma | Pacote de gestão | Repositório |
| --- | --- | --- |
| .NET Standard | [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
