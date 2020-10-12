---
title: Ligações da grelha de eventos Azure para funções Azure
description: Entenda como lidar com eventos de Grade de Eventos em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 1dc1198ab9c546ef1defa1891777c4c8081171e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85957183"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Ligações da grelha de eventos Azure para funções Azure

Esta referência explica como lidar com eventos [de Grade de Eventos](../event-grid/overview.md) em Funções Azure. Para obter mais informações sobre como lidar com as mensagens de Grade de Eventos num ponto final HTTP, consulte [receber eventos num ponto final HTTP](../event-grid/receive-events.md).

Event Grid é um serviço Azure que envia pedidos HTTP para notificá-lo sobre eventos que ocorrem em *editores.* Um editor é o serviço ou recurso que origina o evento. Por exemplo, uma conta de armazenamento de blob Azure é uma editora, e [um upload ou eliminação de bolhas é um evento](../storage/blobs/storage-blob-event-overview.md). Alguns [serviços da Azure têm apoio integrado para a publicação de eventos para a Grade de Eventos.](../event-grid/overview.md#event-sources)

Os *manipuladores de eventos* recebem e processam eventos. A Azure Functions é um dos vários [serviços da Azure que têm suporte integrado para lidar com eventos da Grade de Eventos.](../event-grid/overview.md#event-handlers) Nesta referência, aprende a utilizar um gatilho de Grade de Eventos para invocar uma função quando um evento é recebido da Grade de Eventos, e a utilizar a ligação de saída para enviar eventos para um [tópico personalizado da Grade de Eventos.](../event-grid/post-to-custom-topic.md)

Se preferir, pode utilizar um gatilho HTTP para lidar com eventos de grelha de eventos; ver [Receber eventos num ponto final HTTP](../event-grid/receive-events.md). Atualmente, não é possível utilizar um gatilho de Grade de Eventos para uma aplicação Azure Functions quando o evento é entregue no [esquema cloudEvents.](../event-grid/cloudevents-schema.md#azure-functions) Em vez disso, utilize um gatilho HTTP.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando um evento de Grade de Evento é despachado | [Acionador](./functions-bindings-event-grid-trigger.md) |
| Envia um evento de Grade de Eventos |[Ligação de saída](./functions-bindings-event-grid-output.md) |

O código desta referência é desresíduo à sintaxe .NET Core, utilizada na versão 2.x e superior das Funções. Para obter informações sobre a sintaxe de 1.x, consulte os [modelos de funções de 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Adicione à sua aplicação Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as ligações requer que você refira o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é utilizado para todos os outros tipos de aplicação.

| Idioma                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 2.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registar o [pacote de extensão]          | Recomenda-se a utilização da [extensão Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) com Código de Estúdio Visual. |
| C# Script (online-only in Azure portal)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de republicar a sua aplicação de função, consulte [Atualizar as suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Atualize as suas extensões]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As aplicações Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Passos seguintes
* [Executar uma função quando um evento de Grade de Evento é despachado](./functions-bindings-event-grid-trigger.md)
* [Despachar um evento de Grade de Eventos](./functions-bindings-event-grid-trigger.md)
