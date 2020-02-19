---
title: Ligações da Grelha de Eventos Azure para funções azure
description: Entenda como lidar com eventos de grade de eventos no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461084"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Ligações da Grelha de Eventos Azure para funções azure

Esta referência explica como lidar com eventos da Grelha de [Eventos](../event-grid/overview.md) em Funções Azure. Para mais detalhes sobre como lidar com as mensagens da Grelha de Eventos num ponto final http, consulte [Receber eventos num ponto final http](../event-grid/receive-events.md).

Event Grid é um serviço Azure que envia pedidos http para notificá-lo sobre eventos que acontecem em *editores*. Um Publicador é o serviço ou recurso que origina o evento. Por exemplo, uma conta de armazenamento de blob Azure é uma editora, e [um upload ou eliminação blob é um evento](../storage/blobs/storage-blob-event-overview.md). Alguns [serviços azure têm apoio integrado para a publicação de eventos à Event Grid.](../event-grid/overview.md#event-sources)

Os *manipuladores de eventos* recebem e processam eventos. A Azure Functions é um dos vários [serviços Azure que têm suporte integrado para lidar com eventos da Rede de Eventos.](../event-grid/overview.md#event-handlers) Nesta referência, você aprende a usar um gatilho de Grelha de Eventos para invocar uma função quando um evento é recebido da Grelha de Eventos, e usar a ligação de saída para enviar eventos para um tópico personalizado da Grelha de [Eventos](../event-grid/post-to-custom-topic.md).

Se preferir, pode utilizar um gatilho HTTP para lidar com eventos da Grelha de Eventos; ver [Receber eventos para um ponto final HTTP](../event-grid/receive-events.md). Atualmente, não é possível utilizar um gatilho da Grelha de Eventos para uma aplicação De Funções Azure quando o evento é entregue no [esquema CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Em vez disso, use um gatilho HTTP.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando um evento da Grelha de Eventos é despachado | [Acionador](./functions-bindings-event-grid-trigger.md) |
| Envia um evento de Grelha de Eventos |[Encadernação de saída](./functions-bindings-event-grid-output.md) |

O código nesta referência não se aplica à sintaxe .NET Core, utilizada na versão 2.x e superior das Funções. Para obter informações sobre a sintaxe 1.x, consulte os modelos de [funções 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Adicione à sua app Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e encadernações requer que consulte o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicações.

| Idioma                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C#Script, Java, JavaScript, Python, PowerShell | Registando o [pacote de extensão]          | Recomenda-se a [extensão Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) para utilizar com o Código do Estúdio Visual. |
| C#Script (apenas online no portal Azure)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de voltar a publicar a sua aplicação de funções, consulte [Atualize as suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Atualize as suas extensões]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As funções 1.x apps têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Passos seguintes
* [Executar uma função quando um evento da Grelha de Eventos é despachado](./functions-bindings-event-grid-trigger.md)
* [Despachar um evento da Grelha de Eventos](./functions-bindings-event-grid-trigger.md)
