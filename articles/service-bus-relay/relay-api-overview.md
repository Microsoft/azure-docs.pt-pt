---
title: Visão geral da API de retransmissão do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral das APIs de retransmissão do Azure disponíveis (.NET Standard, .NET Framework, Node. js, etc.)
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 41d9e2026c19c959dc6fe2546b0ef699571ec7cd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513971"
---
# <a name="available-relay-apis"></a>APIs de retransmissão disponíveis

## <a name="runtime-apis"></a>APIs de tempo de execução

A tabela a seguir lista todos os clientes de tempo de execução de retransmissão disponíveis no momento.

A seção [informações adicionais](#additional-information) contém mais informações sobre o status de cada biblioteca de tempo de execução.

| Linguagem/plataforma | Recurso disponível | Pacote de cliente | Repositório |
| --- | --- | --- | --- |
| .NET Standard | Ligações Híbridas | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Reencaminhamento do WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| Nó | Ligações Híbridas | [WebSockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Solicitações HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informação adicional

#### <a name="net"></a>.NET

O ecossistema .NET tem vários tempos de execução, portanto, há várias bibliotecas .NET para a retransmissão. A biblioteca de .NET Standard pode ser executada usando o .NET Core ou o .NET Framework, enquanto a biblioteca de .NET Framework só pode ser executada em um ambiente .NET Framework. Para obter mais informações sobre o .NET Framework, consulte [versões do Framework](/dotnet/articles/standard/frameworks).

A biblioteca de .NET Framework só dá suporte ao modelo de programação do WCF e se baseia em um protocolo binário proprietário com base no transporte de `net.tcp` do WCF. Esse protocolo e a biblioteca são mantidos para compatibilidade com versões anteriores com aplicativos existentes.

A biblioteca de .NET Standard baseia-se na definição de protocolo aberto para a retransmissão de Conexões Híbridas que se baseia em HTTP e WebSockets. A biblioteca dá suporte a uma abstração de fluxo sobre WebSockets e um gesto de API de solicitação-resposta simples para responder a solicitações HTTP. O exemplo de [API da Web](https://github.com/Azure/azure-relay-dotnet) mostra como integrar Conexões Híbridas com ASP.NET Core para serviços Web.

#### <a name="nodejs"></a>Node.js

Os módulos de Conexões Híbridas listados na tabela acima substituem ou coexistem módulos node. js existentes com implementações alternativas que escutam no serviço de retransmissão do Azure em vez da pilha de rede local.

O módulo `hyco-https` se modifique e substitui parcialmente os principais módulos node. js `http` e `https`, fornecendo uma implementação de ouvinte HTTPS compatível com muitos módulos e aplicativos existentes do node. js que dependem desses módulos principais.

Os módulos `hyco-ws` e `hyco-websocket` alteram os módulos populares de `ws` e `websocket` para node. js, fornecendo implementações alternativas de ouvinte que permitem que módulos e aplicativos dependam de qualquer um dos módulos para trabalhar por trás da retransmissão de Conexões Híbridas.

Os detalhes sobre esses módulos podem ser encontrados no repositório GitHub [do Azure-Relay-node](https://github.com/Azure/azure-relay-node) .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a retransmissão do Azure, visite estes links:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.md)
