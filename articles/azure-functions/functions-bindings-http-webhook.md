---
title: Enlaces e acionadores HTTP das Funções do Azure
description: Aprenda a utilizar gatilhos e encadernações HTTP em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77462110"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Funções Azure HTTP desencadeia e encaderna a visão geral

As Funções Azure podem ser invocadas através de pedidos HTTP para construir APIs sem servidor e responder a [webhooks](https://en.wikipedia.org/wiki/Webhook).

| Ação | Tipo |
|---------|---------|
| Executar uma função a partir de um pedido HTTP | [Acionador](./functions-bindings-http-webhook-trigger.md) |
| Devolver uma resposta HTTP a partir de uma função |[Encadernação de saída](./functions-bindings-http-webhook-output.md) |

O código deste artigo não se aplica à sintaxe .NET Core, utilizada na versão 2.x e superior das Funções. Para obter informações sobre a sintaxe 1.x, consulte os modelos de [funções 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Adicione à sua app Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e encadernações requer que consulte o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicações.

| Idioma                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registando o [pacote de extensão]          | Recomenda-se a [extensão Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) para utilizar com o Código do Estúdio Visual. |
| C# Script (apenas online no portal Azure)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de voltar a publicar a sua aplicação de funções, consulte [atualizar as suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Atualize as suas extensões]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As funções 1.x apps têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função a partir de um pedido HTTP](./functions-bindings-http-webhook-trigger.md)
- [Devolver uma resposta HTTP a partir de uma função](./functions-bindings-http-webhook-output.md)
