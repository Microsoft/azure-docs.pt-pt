---
title: Descrição geral da API de reencaminhamento do Azure | Documentos da Microsoft
description: Descrição geral de APIs de reencaminhamento do Azure disponível
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
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 05d7ac56d6c1c48125eb458d0eee852ba396b300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593345"
---
# <a name="available-relay-apis"></a>APIs de reencaminhamento disponíveis

## <a name="runtime-apis"></a>APIs de tempo de execução

A tabela seguinte apresenta uma lista de todos os clientes de tempo de execução de reencaminhamento atualmente disponíveis.

O [informações adicionais](#additional-information) seção contém mais informações sobre o estado de cada biblioteca de tempo de execução.

| Idioma/plataforma | Funcionalidade de disponibilidade | Pacote de cliente | Repositório |
| --- | --- | --- | --- |
| .NET Standard | Ligações Híbridas | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Reencaminhamento do WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| Nó | Ligações Híbridas | [Websockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Pedidos HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

Ecossistema do .NET tem vários tempos de execução, pelo que existem várias bibliotecas de .NET para o reencaminhamento. A biblioteca .NET Standard pode ser executada usando o .NET Core ou .NET Framework, enquanto a biblioteca do .NET Framework só pode ser executada num ambiente do .NET Framework. Para obter mais informações sobre estruturas de .NET, consulte [versões de estrutura](/dotnet/articles/standard/frameworks).

Biblioteca do .NET Framework só suporta o modelo de programação do WCF e depende de um protocolo binário proprietário com base no WCF `net.tcp` transporte. Este protocolo e a biblioteca é mantido para efeitos compatibilidade com aplicativos existentes.

A biblioteca .NET Standard baseia-se a definição de protocolo aberto para o reencaminhamento de ligações híbridas que se baseia no HTTP e WebSockets. A biblioteca suporta uma abstração de fluxo através de Websockets e um gesto de API simples de solicitação-resposta para pedidos HTTP respondendo. O [Web API](https://github.com/Azure/azure-relay-dotnet) exemplo mostra como integrar as ligações híbridas com o ASP.NET Core para serviços da web.

#### <a name="nodejs"></a>Node.js

Os módulos de ligações híbridas listados na tabela acima, substituam ou corrigir módulos existentes do node. js com implementações alternativos que escutar o serviço de reencaminhamento do Azure em vez da pilha de rede local.

O `hyco-https` módulo amends e parcialmente substitui os módulos do node. js principais `http` e `https`, fornecendo uma implementação de serviço de escuta HTTPS é compatível com vários módulos node. js existente e aplicações que dependem estas principais módulos.

O `hyco-ws` e `hyco-websocket` módulos corrigir o popular `ws` e `websocket` módulos para node. js, fornecendo as implementações de serviço de escuta alternativo que ativar módulos e aplicativos que dependam de qualquer módulo para funcionar por trás da híbrida Ligações de reencaminhamento.

Detalhes sobre esses módulos podem ser encontrados no [nó do reencaminhamento do azure](https://github.com/Azure/azure-relay-node) repositório do GitHub.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o reencaminhamento do Azure, visite estas ligações:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.md)
