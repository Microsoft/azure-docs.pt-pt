---
title: Ligações de autocarros Azure Service para funções Azure
description: Aprenda a enviar gatilhos e encadernações de autocarro da Azure Service em Funções Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 46660a0c8d20ab82c994a62b1c781108ea1070c1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111206"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Ligações de autocarros Azure Service para funções Azure

A Azure Functions integra-se com [o Azure Service Bus](https://azure.microsoft.com/services/service-bus) através de [gatilhos e encadernações.](./functions-triggers-bindings.md) A integração com o Service Bus permite-lhe construir funções que reajam e enviem mensagens de fila ou tópicos.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando uma fila de ônibus de serviço ou mensagem de tópico é criada | [Acionador](./functions-bindings-service-bus-trigger.md) |
| Enviar mensagens de autocarro da Azure Service |[Ligação de saída](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione à sua aplicação Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as ligações requer que você refira o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é utilizado para todos os outros tipos de aplicação.

| Linguagem                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 4.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registar o [pacote de extensão]          | Recomenda-se a utilização da [extensão Azure Tools] com Código de Estúdio Visual. |
| C# Script (online-only in Azure portal)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de republicar a sua aplicação de função, consulte [Atualizar as suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize as suas extensões]: ./install-update-binding-extensions-manual.md
[Extensão de Ferramentas Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As aplicações functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Próximos passos

- [Executar uma função quando uma fila de autocarro de serviço ou mensagem de tópico é criada (Trigger)](./functions-bindings-service-bus-trigger.md)
- [Enviar mensagens de autocarro da Azure Service de Azure Functions (ligação de saída)](./functions-bindings-service-bus-output.md)
