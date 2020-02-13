---
title: Visão geral da API dos hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral das APIs disponíveis (tempo de execução e gerenciamento) para usar o serviço de hubs de eventos do Azure.
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162230"
---
# <a name="available-event-hubs-apis"></a>APIs de hubs de eventos disponíveis

Este artigo descreve o conjunto de clientes de API disponíveis que você pode usar para gerenciar recursos de hubs de eventos.

## <a name="runtime-apis"></a>APIs de tempo de execução

A seção a seguir descreve todos os clientes de tempo de execução dos hubs de eventos do Azure disponíveis atualmente. Embora algumas destas bibliotecas também incluam funcionalidades de gestão limitadas, existem também [bibliotecas específicas dedicadas](#management-apis) a operações de gestão. O foco principal dessas bibliotecas é enviar e receber mensagens de um hub de eventos.

Para obter mais informações sobre o estado atual de cada biblioteca de tempo de execução, consulte [informações adicionais](#additional-information).

| Linguagem/plataforma | Pacote de cliente | Pacote EventProcessorHost | Repositório |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/D |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Nó | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/D | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | N/D | N/D | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

O ecossistema .NET tem vários tempos de execução, portanto, há várias bibliotecas .NET para hubs de eventos. A biblioteca de .NET Standard pode ser executada usando o .NET Core ou o .NET Framework, enquanto a biblioteca de .NET Framework só pode ser executada em um ambiente .NET Framework. Para obter mais informações sobre as versões .NET Framework, consulte [versões-quadro](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Nó

A [biblioteca JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) encontra-se atualmente em pré-visualização e é mantida como um projeto paralelo por funcionários da Microsoft e colaboradores externos. Todas as contribuições que incluem o código-fonte são boas-vindas e serão revisadas.

## <a name="management-apis"></a>APIs de gestão

A tabela a seguir lista todas as bibliotecas específicas de gerenciamento disponíveis no momento. Nenhuma dessas bibliotecas contém operações de tempo de execução e são para o único propósito de gerenciar entidades de hubs de eventos.

| Linguagem/plataforma | Pacote de gerenciamento | Repositório |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
