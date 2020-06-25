---
title: Visão geral da AZure Relay API / Microsoft Docs
description: Este artigo fornece uma visão geral das APIs disponíveis do Azure Relay (.NET Standard, .NET Framework, Node.js, etc.)
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 15e13ccac9b05e6e1b0730b38b372dec20c69dda
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85316951"
---
# <a name="available-relay-apis"></a>APIs de retransmissão disponível

## <a name="runtime-apis"></a>APIs de tempo de execução

A tabela que se segue lista todos os clientes de reencaminhadores atualmente disponíveis.

A secção [de informações adicionais](#additional-information) contém mais informações sobre o estado de cada biblioteca de tempo de execução.

| Idioma/Plataforma | Recurso disponível | Pacote de cliente | Repositório |
| --- | --- | --- | --- |
| .NET Standard | Ligações Híbridas | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Reencaminhamento do WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/D |
| Nó | Ligações Híbridas | [WebSockets:`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets:`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP Pedidos:`hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

O ecossistema .NET tem vários tempos de funcionação, portanto existem múltiplas bibliotecas .NET para o Relé. A biblioteca standard .NET pode ser executada utilizando o núcleo .NET ou o quadro .NET, enquanto a biblioteca .NET Framework só pode ser executada num ambiente quadro .NET. Para obter mais informações sobre os quadros .NET, consulte as [versões-quadro](/dotnet/articles/standard/frameworks).

A biblioteca.NET Framework apenas suporta o modelo de programação do WCF e baseia-se num protocolo binário proprietário baseado no `net.tcp` transporte wcf. Este protocolo e biblioteca é mantido para retrocompatibilidade com as aplicações existentes.

A biblioteca .NET Standard baseia-se na definição de protocolo aberto para o Relay de Conexões Híbridas que se baseia em HTTP e WebSockets. A biblioteca suporta uma abstração de fluxo sobre WebSockets e um simples gesto API de resposta a pedidos para responder a pedidos HTTP. A amostra [da Web API](https://github.com/Azure/azure-relay-dotnet) mostra como integrar ligações híbridas com ASP.NET Core para serviços web.

#### <a name="nodejs"></a>Node.js

Os módulos de Conexões Híbridas listados na tabela acima substituem ou alterem os módulos de Node.js existentes por implementações alternativas que ouvem o serviço Azure Relay em vez da pilha de rede local.

O `hyco-https` módulo altera e substitui parcialmente os módulos de Node.js `http` `https` e, fornecendo uma implementação https que é compatível com muitos módulos e aplicações Node.js existentes que dependem destes módulos centrais.

Os `hyco-ws` `hyco-websocket` módulos e módulos alteram os `ws` populares e os `websocket` módulos para Node.js, proporcionando implementações alternativas de ouvintes que permitem que módulos e aplicações que dependam de qualquer módulo funcione atrás do Relé de Conexões Híbridas.

Detalhes sobre esses módulos podem ser encontrados no repositório [azure-relay-node](https://github.com/Azure/azure-relay-node) GitHub.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Relay, visite estes links:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.md)
