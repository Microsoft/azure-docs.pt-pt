---
title: Enlaces do Service Bus do Azure para as funções do Azure
description: Aprenda a enviar gatilhos e encadernações azure service bus em Funções Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277417"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Enlaces do Service Bus do Azure para as funções do Azure

A Azure Functions integra-se com o [Azure Service Bus](https://azure.microsoft.com/services/service-bus) através de [gatilhos e encadernações.](./functions-triggers-bindings.md) Integrar-se com o Service Bus permite-lhe construir funções que reagem e enviem mensagens de fila ou tópicos.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando uma fila de ônibus de serviço ou mensagem tópico é criada | [Acionador](./functions-bindings-service-bus-trigger.md) |
| Envie mensagens de ônibus de serviço azure |[Encadernação de saída](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione à sua app Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e encadernações requer que consulte o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicações.

| Linguagem                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 4.x | |
| C#Script, Java, JavaScript, Python, PowerShell | Registando o [pacote de extensão]          | Recomenda-se a [Extensão de ferramentas azure] para utilizar com o Código do Estúdio Visual. |
| C#Script (apenas online no portal Azure)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de voltar a publicar a sua aplicação de funções, consulte [Atualize as suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize as suas extensões]: ./install-update-binding-extensions-manual.md
[Extensão de ferramentas azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As funções 1.x apps têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Passos Seguintes

- [Executar uma função quando uma fila de ônibus de serviço ou mensagem tópico é criada (Gatilho)](./functions-bindings-service-bus-trigger.md)
- [Envie mensagens de ônibus de serviço Azure a partir de funções Azure (encadernação de saída)](./functions-bindings-service-bus-output.md)
