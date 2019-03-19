---
title: Descrição geral de API de Hubs de eventos do Azure | Documentos da Microsoft
description: Descrição geral de APIs de Hubs de eventos do Azure disponível
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
ms.openlocfilehash: dcfc2480cd9a62b57255163132373998db328034
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075804"
---
# <a name="available-event-hubs-apis"></a>APIs de Hubs de eventos disponíveis

Este artigo descreve o conjunto de clientes de API disponíveis, que pode utilizar para gerir recursos de Hubs de eventos.

## <a name="runtime-apis"></a>APIs de tempo de execução

A secção seguinte descreve todos os clientes de tempo de execução de Event Hubs do Azure atualmente disponíveis. Embora algumas dessas bibliotecas também incluem a funcionalidade de gestão limitada, há também [bibliotecas específicas](#management-apis) dedicados às operações de gestão. O foco principal dessas bibliotecas é enviar e receber mensagens de um hub de eventos.

Para obter mais informações sobre o estado atual de cada biblioteca de tempo de execução, consulte [informações adicionais](#additional-information).

| Idioma/plataforma | Pacote de cliente | Pacote de EventProcessorHost | Repositório |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/A |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Nó | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | N/A | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

Ecossistema do .NET tem vários runtimes, pelo que existem várias bibliotecas do .NET dos Hubs de eventos. A biblioteca .NET Standard pode ser executada usando o .NET Core ou .NET Framework, enquanto a biblioteca do .NET Framework só pode ser executada num ambiente do .NET Framework. Para obter mais informações sobre as versões do .NET Framework, consulte [versões de estrutura](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Nó

O [biblioteca node. js](https://github.com/Azure/azure-event-hubs-node) está atualmente em pré-visualização e é mantido como um projeto de lado por funcionários da Microsoft e os contribuintes externos. Todas as contribuições, incluindo código-fonte são boas-vindas e serão revistas.

## <a name="management-apis"></a>APIs de gestão

A tabela seguinte lista todas as bibliotecas de gestão específico atualmente disponíveis. Nenhuma dessas bibliotecas conter operações de tempo de execução e destinam-se a única finalidade da gestão de entidades de Hubs de eventos.

| Idioma/plataforma | Pacote de gestão | Repositório |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Passos Seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
