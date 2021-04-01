---
title: Enlaces e acionadores HTTP das Funções do Azure
description: Aprenda a utilizar gatilhos HTTP e encadernações em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 5236a3be9f8908d886274764dfc0c0da5b3565bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104483"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Funções Azure HTTP dispara e encaderna mente geral

As Funções Azure podem ser invocadas através de pedidos HTTP para construir APIs sem servidor e responder a [webhooks](https://en.wikipedia.org/wiki/Webhook).

| Ação | Tipo |
|---------|---------|
| Executar uma função a partir de um pedido HTTP | [Acionador](./functions-bindings-http-webhook-trigger.md) |
| Devolver uma resposta HTTP de uma função |[Ligação de saída](./functions-bindings-http-webhook-output.md) |

O código deste artigo é predefinido para a sintaxe .NET Core, utilizada na versão 2.x e superior das Funções. Para obter informações sobre a sintaxe de 1.x, consulte os [modelos de funções de 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Adicione à sua aplicação Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as ligações requer que você refira o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é utilizado para todos os outros tipos de aplicação.

| Linguagem                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registar o [pacote de extensão]          | Recomenda-se a utilização da [extensão Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) com Código de Estúdio Visual. |
| C# Script (online-only in Azure portal)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de republicar a sua aplicação de função, consulte [Atualizar as suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Atualize as suas extensões]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As aplicações Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função a partir de um pedido HTTP](./functions-bindings-http-webhook-trigger.md)
- [Devolver uma resposta HTTP de uma função](./functions-bindings-http-webhook-output.md)