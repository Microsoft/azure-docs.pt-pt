---
title: Visão geral da API do Relé Azure [ Microsoft Docs
description: Este artigo fornece uma visão geral das APIs disponíveis do Azure Relay (.NET Standard, .NET Framework, Node.js, etc.)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76513971"
---
# <a name="available-relay-apis"></a>APIs de retransmissão disponível

## <a name="runtime-apis"></a>APIs de tempo de execução

A tabela seguinte lista todos os clientes de tempo de execução relay atualmente disponíveis.

A secção de [informação adicional](#additional-information) contém mais informações sobre o estado de cada biblioteca de tempo de execução.

| Idioma/Plataforma | Recurso disponível | Pacote de cliente | Repositório |
| --- | --- | --- | --- |
| .NET Standard | Ligações Híbridas | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Reencaminhamento do WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/D |
| Nó | Ligações Híbridas | [Websockets:`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets:`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[PEDIDOS HTTP:`hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

O ecossistema .NET tem vários tempos de execução, pelo que existem várias bibliotecas .NET para o Relé. A biblioteca .NET Standard pode ser executada utilizando o .NET Core ou o .NET Framework, enquanto a biblioteca .NET Framework só pode ser executada num ambiente de enquadramento .NET. Para obter mais informações sobre os quadros .NET, consulte [versões-quadro](/dotnet/articles/standard/frameworks).

A biblioteca-quadro .NET apenas suporta o modelo de programação wCF e baseia-se `net.tcp` num protocolo binário proprietário baseado no transporte wCF. Este protocolo e biblioteca mantém-se para retrocompatibilidade com aplicações existentes.

A biblioteca .NET Standard baseia-se na definição de protocolo aberto para o Relé de Ligações Híbridas que se baseia em HTTP e WebSockets. A biblioteca suporta uma abstração de fluxo sobre Websockets e um simples gesto de Resposta de Pedido-Resposta API para responder a pedidos http. A amostra [web API](https://github.com/Azure/azure-relay-dotnet) mostra como integrar ligações híbridas com ASP.NET Core para serviços web.

#### <a name="nodejs"></a>Node.js

Os módulos De Conexões Híbridas listados na tabela acima substituem ou alterem os módulos nonómetros existentes com implementações alternativas que ouvem o serviço De retransmissão Azure em vez da pilha de rede local.

O `hyco-https` módulo altere e sobrepõe parcialmente os módulos `http` `https`de node.js e, proporcionando uma implementação de ouvintes HTTPS compatível com muitos módulos e aplicações nó.js existentes que dependem destes módulos centrais.

Os `hyco-ws` `hyco-websocket` módulos e módulos alterem os populares `ws` e `websocket` os módulos para o Node.js, proporcionando implementações alternativas de ouvintes que permitem que módulos e aplicações dependam de qualquer módulo para trabalhar por trás do Relay de Ligações Híbridas.

Detalhes sobre esses módulos podem ser encontrados no repositório GitHub de [retransmissão azul.](https://github.com/Azure/azure-relay-node)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Relay, visite estes links:
* [O que é Azure Relay?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.md)
